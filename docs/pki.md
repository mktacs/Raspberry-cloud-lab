## Public Key Infrastructure (PKI) setup for OpenVPN
### 1. Tools used
- OpenVPN (configuration notes are located at [openvpn.md](openvpn.md))
- Easy-RSA
---
- Installed Easy-RSA and OpenVPN on the PI using:
```bash
sudo apt update
sudo apt install -y openvpn easy-rsa
```
### 2. PKI Workspace initialization
- Copied Easy-RSA scripts to the home folder from the system location
```bash
sudo mkdir ~/easyrsa
cp -r /usr/share/easy-rsa/* ~/easyrsa/
cd ~/easyrsa
```
- Then initialized the PKI directory structure via `./easyrsa init-pki`
	- this created a pki/ directory, which stores all certificates, keys, and requests
### 3. Certificate Authority (CA) creation
- A private CA was created to sign all vpn certificates via `./easyrsa build-ca`
	- set a CA's common name and passphrase
- Generated files:
	- `pki/ca.crt` - public CA certificate
	- `pki/private/ca.key` - private CA key (must be secured and never leave the server)
- The CA key is used only for signing certificates and is not used by OpenVPN directly.1~The CA key is used only for signing certificates and is not used by OpenVPN directly
### 4. OpenVPN server certificate
- A server key and certificate request were generated via `./easyrsa gen-req server nopass`
- The request was signed by the CA via `./easyrsa sign-req server server`
- Generated files: 
	- `pki/issued/server.crt`
	- `pki/private/server.key`
	- These files identify the VPN server to clients
### 5. VPN client certificates
- A unique certificate was generated per client device (e.g. laptop, phone):
```bash
./easyrsa gen-req <client-name> nopass
./easyrsa sign-req client <client-name>
```
- Generated files per client:
	- `pki/issued/<client-name>.crt`
	- `pki/private/<client-name>.key`
- Each device must have its own certificate to allow individual revocation
### 6. TLS handshake protection
- A static TLS key was generated to protect the OpenVPN TLS handshake via `openvpn --genkey secret ta.key`
- This key:
	- prevents unauthenticated packets from reaching OpenVPN
	- protects against port scanning and DoS
	- is shared between server and authorized clients
> ta.key is not the CA key, it is a symmetric key used only for OpenVPN TLS protection
### 7. Diffie-Hellman parameters
- DH parameters were generated via `./easyrsa gen-dh`
- Generated file:
	- `pki/dh.pem` - used for secure key exchange during TLS setup
### 8. Certificate Revocation List (CRL)
- A CRL was generated to allow revoking client certificates via `./easyrsa gen-crl`
- Generated file:
	- `pki/crl.pem` - the CRL is checked by OpenVPN on every client connection
### 9. Files deployed to OpenVPN directory
- Only server-side files were copied to `/etc/openvpn/`:
```bash
sudo cp pki/ca.crt pki/dh.pem ta.key pki/crl.pem /etc/openvpn/
sudo cp pki/issued/server.crt pki/private/server.key /etc/openvpn/
```
- Client private keys were not copied to the OpenVPN directory
- Restricted permissions:
```bash
sudo chmod 600 /etc/openvpn/server.key
sudo chmod 644 /etc/openvpn/ca.crt
sudo chmod 644 /etc/openvpn/server.crt
sudo chmod 644 /etc/openvpn/crl.pem
```
