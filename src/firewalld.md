# firewalld

> Run on both `sdi-1` and `sdi-2`; adjust the values accordingly.

```shell
ssh jean@sdi-1.alphahorizon.io
sudo apt update
sudo apt install -y firewalld
sudo systemctl enable --now firewalld
sudo firewall-cmd --zone=public --add-interface=eth0 --permanent
sudo firewall-cmd --permanent --add-service=mdns
sudo firewall-cmd --permanent --add-service=llmnr
sudo firewall-cmd --reload
```
