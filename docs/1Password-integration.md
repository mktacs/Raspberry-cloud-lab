## 1Password integration
- 1Password is a cloud password manager, decided to install 1Password CLI with a service account to access secrets related to the PI server
- It would be useful in the future for automatic secret injection
	- Example: container is starting and requires a token, I will utilise 1password for token injection
### 1. 1Password CLI installation
- Used official manual: https://developer.1password.com/docs/cli/get-started/
```bash
curl -sS https://downloads.1password.com/linux/keys/1password.asc | \
  sudo gpg --dearmor --output /usr/share/keyrings/1password-archive-keyring.gpg && \
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/1password-archive-keyring.gpg] https://downloads.1password.com/linux/debian/$(dpkg --print-architecture) stable main" | \
  sudo tee /etc/apt/sources.list.d/1password.list && \
  sudo mkdir -p /etc/debsig/policies/AC2D62742012EA22/ && \
  curl -sS https://downloads.1password.com/linux/debian/debsig/1password.pol | \
  sudo tee /etc/debsig/policies/AC2D62742012EA22/1password.pol && \
  sudo mkdir -p /usr/share/debsig/keyrings/AC2D62742012EA22 && \
  curl -sS https://downloads.1password.com/linux/keys/1password.asc | \
  sudo gpg --dearmor --output /usr/share/debsig/keyrings/AC2D62742012EA22/debsig.gpg && \
  sudo apt update && sudo apt install 1password-cli
```
- verified it is installed via `op --version`
### 2. Creating a service account
- Official manual: https://developer.1password.com/docs/service-accounts/use-with-1password-cli
- Service account can be created on 1Password webpage/settings/developer
- Selected only 1 vault with Read and write access
- Token is generated, it will be saved separately for now until a real use case
