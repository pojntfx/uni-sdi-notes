# SSH

> Run on both `sdi-1` and `sdi-2`; adjust the values accordingly.

```shell
ssh root@sdi-1.alphahorizon.io

apt update
apt install -y sudo curl openssh-server locales
systemctl enable --now ssh

echo 'PermitRootLogin no' | tee /etc/ssh/ssh_config.d/no-root.conf

passwd -d root
passwd -l root
chsh -s /sbin/nologin
rm ~/.ssh/authorized_keys

systemctl restart ssh
```
