# Network

## DNS Entries

```dns
jeans-box     10800   IN      AAAA    2001:7c7:2121:8d00::3
*.jeans-box   10800   IN      AAAA    2001:7c7:2121:8d00::3
jeans-box     10800   IN      A       138.68.70.72
*.jeans-box   10800   IN      A       138.68.70.72
```

## Box

```shell
ssh root@jeans-box
tee /etc/sysctl.d/privacy.conf <<'EOT'
net.ipv6.conf.all.use_tempaddr=2
EOT
sysctl -p

tee /etc/network/interfaces.d/eth0 <<'EOT'
auto eth0
iface eth0 inet dhcp

iface eth0 inet6 static
    address 2001:7c7:2121:8d00::3
    autoconf 1
    accept_ra 2
EOT
systemctl restart networking

tee /etc/resolv.conf <<'EOT'
nameserver 2606:4700:4700::1111
nameserver 2606:4700:4700::1001
EOT
chattr +i /etc/resolv.conf
sed -i /etc/hosts -e 's/\tlocalhost/\tlocalhost jeans-box/g'
```
