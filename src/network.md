# Network

## Box

> Run on both `sdi-1` and `sdi-2`; adjust the values accordingly.

```shell
ssh root@sdi-1
tee /etc/sysctl.d/privacy.conf <<'EOT'
net.ipv6.conf.all.use_tempaddr=2
EOT
sysctl -p

tee /etc/network/interfaces.d/eth0 <<'EOT'
auto eth0
iface eth0 inet dhcp

iface eth0 inet6 static
    address 2604:a880:cad:d0::d40:1001
    autoconf 1
    accept_ra 2
EOT
systemctl restart networking

tee /etc/resolv.conf <<'EOT'
nameserver 2606:4700:4700::1111
nameserver 2606:4700:4700::1001
EOT
chattr +i /etc/resolv.conf
sed -i /etc/hosts -e 's/\tlocalhost/\tlocalhost sdi-1/g'
```

## DNS Entries

```dns
sdi-1     10800   IN      AAAA    2604:a880:cad:d0::d40:1001
*.sdi-1   10800   IN      AAAA    2604:a880:cad:d0::d40:1001
sdi-1     10800   IN      A       143.198.37.173
*.sdi-1   10800   IN      A       143.198.37.173
sdi-2     10800   IN      AAAA    2400:6180:0:d0::1020:c001
*.sdi-2   10800   IN      AAAA    2400:6180:0:d0::1020:c001
sdi-2     10800   IN      A       128.199.92.39
*.sdi-2   10800   IN      A       128.199.92.39
```
