# Cockpit

```shell
echo 'deb http://deb.debian.org/debian bullseye-backports main' | sudo tee /etc/apt/sources.list.d/backports.list
sudo apt update
sudo apt install -t bullseye-backports -y cockpit cockpit-podman cockpit-pcp

curl https://cockpit.jeans-box.example.com/ # Test Cockpit
```
