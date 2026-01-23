## Docker setup
- Installed docker via official manual: https://docs.docker.com/engine/install/ubuntu/
- verified it is running and enabled via `sudo systemctl status docker`
- tested via `sudo docker run hello-world`
- added my user to a docker group for convenience via `sudo usermod -aG docker $USER`
	- exited from ssh and logged back in, now docker commands can be ran without `sudo`
- verified whether docker interface has been created via `ip a`
