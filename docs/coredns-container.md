##CoreDNS service deployment via docker
### 1. Initial configuration
- Created a "containers/coredns" folders in my home directory
	- coredns folder contains "Corefile" with config for the dns service and docker-compose.yml with configurations for the container itself
- Created another folder named "zones", and "pi.db" inside of it (it will contain all resolutions of my local domain to ip addresses)
- examples of "Corefile", "docker-compose.yml" and "pi.db" with comments can be found in [/configs/](../configs)
### 2. Deploying the container
- `docker compose up -d`
- verifying it is running via `docker ps`
- added my tun ip as a main dns option to openvpn's [server config](../configs/server.conf) 
- restarted openvpn service, and tested if `nslookup vpn.pi` resolves a 10.8.0.1 ip - all good
	- also tried to take ssh access via ssh <user>@ssh.pi - also works
### (Troubleshooting I had to do before step #2)
- at first I couldn't deploy a container with specified ports in a [docker-compose.yml](../configs/docker-compose.yml) because systemd-resolver was already listening to port 53
- in order to fix it, disabled `DNSStubListener=no` in `resolver.conf` (`sudo nano /etc/systemd/resolved.conf`) and restarted the service via `sudo systemctl restart systemd-resolved`
	- additionally, removed resolv.conf completely via `sudo rm /etc/resolv.conf` and linked it back via `sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf`
- afterwards systemd resolver wasn't listening to port 53 anymore (can be verified via `ss -tulnep`, but after restarting a coredns container, it didn't help
	- removed container and a network via `docker compose down coredns` and started again via `docker compose up -d`
- now container properly listens to port 53 and everything works
