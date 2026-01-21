## Default policies
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
- those are default policies where all incoming traffic is denied and all outgoing is allowed
- current config can be verified via `sudo ufw status verbose`
## SSH access & enabling the firewall
- since we are denying all incoming traffic, SSH will be denied as well
	- in order to allow ssh traffic ran the following `sudo ufw allow 22/tcp`
		- basically we are allowing incoming tcp traffic via tcp port 22, which is running ssh
- this is not ideal, so after setting up a VPN service, made the changes in the next changes
- `sudo ufw enable` in order to enable the firewall
## After VPN setup
- setting a rule which allows all incoming traffic through tun0 port (vpn)
	- `sudo ufw allow in on tun0`
- setting a rule which allows incoming traffic on 1194 UDP port (OpenVPN service)
	- `sudo ufw allow in 1194/udp`
- removing every other rule via `sudo ufw delete <rule>`
- now only port 1194 is accessible to the public, but once you are connected to the VPN your traffic is trusted and allowed
- it would be better to allow only required ports even when connected to the VPN, but there is not much services at the moment so it's fine for now, will modify it later
