### 1. Installation
- OpenVPN was installed together with Easy-RSA, documented in [pki.md](pki.md)
- Also all required server-side certs were copied to the OpenVPN directory
### 2. VPN protocol and mode
- Protocol: UDP
- Tunnel type: tun (Layer 3, routed VPN)
- Authentication: Mutual TLS (server + client certificates)
- Handshake protection: tls-crypt
### 3. Server configuration
- The main OpenVPN configuration file is located at:
	- `/etc/openvpn/server.conf`
- The exact configuration file can be found here: [server.conf](../configs/server.conf)
- Key configurations:
	- proto udp - chooses the protocol to use
	- dev tun - sets the mode to tun instead of tap
	- topology subnet - sets the topology to subnet instead of P2P
	- Full-tunnel routing - all traffic goes through VPN
	- DNS servers pushed to clients
	- TLS minimum version enforced
	- Modern AEAD cipher
	- Server runs as an unprivileged user
### 4. PKI integration
- OpenVPN is integrated with the internal PKI documented in [pki.md](pki.md)
### 5. IP forwarding
- IPv4 forwarding was enabled to allow routing between VPN clients and other networks:
	- `sudo nano /etc/sysctl.conf`
	- Enabled via `net.ipv4.ip_forward=1`
	- Applied via `sudo sysctl -p`
### 6. Firewall configuration
- See the [firewall.md](firewall.md) post-vpn setup section
### 7. NAT configuration
- NAT is required to allow VPN clients to access external networks
- NAT rules were added to `/etc/ufw/before.rules`
```bash
*nat
:POSTROUTING ACCEPT [0:0]
-A POSTROUTING -s 10.8.0.0/24 -o <external-interface> -j MASQUERADE
COMMIT
```
- The external interface corresponds to the default route interface
### 8. Forwarding policy
- UFW forwarding policy was enabled to allow routed traffic:
	- `sudo nano /etc/default/ufw`
	- Set `DEFAULT_FORWARD_POLICY="ACCEPT"`
- This is required for VPN client traffic to be forwarded through the server.
### 9. Service management
- The OpenVPN server is managed via systemd:
```bash
sudo systemctl enable openvpn@server
sudo systemctl start openvpn@server
```
- Status can be checked via `sudo systemctl status openvpn@server`
### 10. Client configuration
- Created a working folder for client bundles: 
```bash
mkdir ~/opvn
chmod 700 ~/ovpn
```
- Create a config file for each client
	- `nano ~/ovpn/<name>.ovpn`
- Example of the redacted config is located here: [client.ovpn](../configs/client.ovpn)
- Certs and keys can be embedded directly to the ovpn file via:
```bash
sed -i "/<ca>/r pki/ca.crt" <path/to/file.ovpn>
sed -i "/<cert>/r pki/issued/<NAME.crt>" <path/to/file.ovpn>
sed -i "/<key>/r pki/private/<NAME.key>" <path/to/file.ovpn>
sed -i "/<tls-crypt>/r /etc/openvpn/ta.key" <path/to/file.ovpn>
```
> Don't touch the "ca/cert/key/tls-crypt" fields. You only need to change <NAME.crt/.key> to the actual name, and <path/to/file.ovpn> to the path to the client ovpn config file
- One of the ways to transfer the client ovpn config to the client securely is `scp`:
	- `scp -i ~/.ssh/<your_key> <your_user>@<pi_lan_ip>:~/ovpn/laptop.ovpn .`
### 11. External access & security properties
- UDP port 1194 is forwarded on the router to the server’s LAN IP
- Clients connect using the server’s public IP address
- No services exposed directly except OpenVPN
- Certificate-based authentication only
- TLS handshake protected with tls-crypt
- Client access can be revoked without affecting others
- Firewall enforces VPN as the trust boundary
### 12. Operational notes
- OpenVPN starts automatically on boot
- After power loss, the server resumes operation without manual intervention
- New clients require new certificates and configs
- Compromised clients can be revoked via the CRL
