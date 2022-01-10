# Podman

```shell
ssh jean@jeans-box.example.com
echo 'deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/Debian_11/ /' | sudo tee /etc/apt/sources.list.d/libcontainers.list
curl -L "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/Debian_11/Release.key" | sudo apt-key add -
sudo apt update
sudo apt upgrade -y # Prevent conflicts with eventual prior Podman install from Debian repos
sudo apt install -t Debian_11 -y podman
echo 'unqualified-search-registries=["docker.io"]' | sudo tee /etc/containers/registries.conf.d/docker.conf
sudo systemctl unmask podman-auto-update.service
sudo systemctl unmask podman-auto-update.timer
sudo systemctl enable --now podman-auto-update.timer
sudo systemctl enable --now podman-restart
```