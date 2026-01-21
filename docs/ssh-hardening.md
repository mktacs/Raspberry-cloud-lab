## Hardening measures implemented
### 1. Keys generation
- SSH keys were generated using the Ed25519 algorithm
 - ran following command on the local machine: `ssh-keygen -t ed25519 -C "<comment>"
 - created a passphrase as well
- Then, in order to add it to the PI, edited "authorized_keys" in ~/.ssh/ folder.
 - on the local machine ran the following in powershell: `type $env:USERPROFILE\.ssh\id_ed25519.pub
 - copied the content and pasted in "authorized_keys"
- these keys must be generated on all devices that should have access to SSH the server
### 2. Editing the ssh configs
- edited **sshd_config** & **50-cloud-init.conf**
 - path to sshd_config - /etc/ssh/
 - path to 50-cloud-init.conf - /etc/ssh/sshd_config.d/
 - edited and uncommented the following:
```bash
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
```
- now ssh is only available on devices with authorized keys and the passphrase must be entered
- password won't work
### 3. VPN only access
- After deploying OpenVPN server, made SSH available only when connected via VPN
- This config is documented in /Raspberry-cloud-lab/docs/firewall.md
