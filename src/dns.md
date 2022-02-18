# DNS

> You can convert the IPv6 addresses to nibble format using e.g. [rdns6.com/nibble](http://rdns6.com/nibble)

## Manager (`sdi-1`)

```shell
ssh jean@sdi-1.alphahorizon.io

sudo apt update
sudo apt install -y bind9 bind9utils
sudo systemctl enable --now named

sudo tee /etc/bind/named.conf.options <<'EOT'
options {
        directory "/var/cache/bind";

        dnssec-validation auto;

        listen-on port 54 { 127.0.0.1; };
        listen-on-v6 port 54 { ::1; };

        version "not currently available";
        recursion yes;
        querylog yes;
        allow-transfer { none; };
        allow-query { any; };
};
EOT

sudo tee /etc/bind/named.conf.local <<'EOT'
include "/etc/bind/zones.rfc1918";

zone "example.pojtinger" {
        type master;
        file "/etc/bind/db.example.pojtinger";
        allow-query { any; };
        allow-transfer { 128.199.92.39; 2400:6180:0:d0::1020:c001; };
};

zone "37.198.143.in-addr.arpa" {
        type master;
        file "/etc/bind/db.37.198.143";
        allow-query { any; };
        allow-transfer { 128.199.92.39; 2400:6180:0:d0::1020:c001; };
};

zone "1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2.ip6.arpa" {
        type master;
        file "/etc/bind/db.1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2";
        allow-query { any; };
        allow-transfer { 128.199.92.39; 2400:6180:0:d0::1020:c001; };
};
EOT

# Increase `1634570712` by one and reload after each change to propagate changes to the worker
sudo tee /etc/bind/db.example.pojtinger <<'EOT'
$ORIGIN example.pojtinger.
$TTL 3600

example.pojtinger.      IN      SOA     ns1.example.pojtinger. hostmaster.example.pojtinger.    ( 1634570712 7200 3600 1209600 3600 )

example.pojtinger.      IN      NS      ns1.example.pojtinger.
example.pojtinger.      IN      NS      ns2.example.pojtinger.

example.pojtinger.      IN      A       143.198.37.173
example.pojtinger.      IN      AAAA    2604:a880:cad:d0::d40:1001

ns1.example.pojtinger.  IN      A       143.198.37.173
ns1.example.pojtinger.  IN      AAAA    2604:a880:cad:d0::d40:1001

ns2.example.pojtinger.  IN      A       128.199.92.39
ns2.example.pojtinger.  IN      AAAA    2400:6180:0:d0::1020:c001

example.pojtinger.      IN      MX      1       fb.mail.gandi.net.
www.example.pojtinger.  IN      CNAME   example.pojtinger.
EOT

# Increase `1634570724` by one and reload after each change to propagate changes to the worker
sudo tee /etc/bind/db.37.198.143 <<'EOT'
$ORIGIN 37.198.143.in-addr.arpa. ; IP is 143.198.37.173, so the suffix is 37.198.143
$TTL 3600

@       IN      SOA     ns1.example.pojtinger. hostmaster.example.pojtinger.    ( 1634570724 7200 3600 1209600 3600 )

@       IN      NS      ns1.example.pojtinger.
@       IN      NS      ns2.example.pojtinger.

173    IN      PTR     example.pojtinger. ; IP is 143.198.37.173, so the prefix is 173
EOT

# Increase `1634570724` by one and reload after each change to propagate changes to the worker
sudo tee /etc/bind/db.1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2 <<'EOT'
$ORIGIN 1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2.ip6.arpa.
$TTL 3600

@       IN      SOA     ns1.example.pojtinger. hostmaster.example.pojtinger.    ( 1634570724 7200 3600 1209600 3600 )

@       IN      NS      ns1.example.pojtinger.
@       IN      NS      ns2.example.pojtinger.

1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2.ip6.arpa.       IN      PTR     example.pojtinger.
EOT

sudo named-checkconf

sudo named-checkzone example.pojtinger /etc/bind/db.example.pojtinger
sudo named-checkzone 37.198.143.in-addr.arpa. /etc/bind/db.37.198.143
sudo named-checkzone 1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2.ip6.arpa. /etc/bind/db.1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2

sudo systemctl reload named

# On the local system
# Test DNS over TCP
dig +noall +answer +tcp @sdi-1.alphahorizon.io example.pojtinger AAAA
# Test DNS over UDP
dig +noall +answer @sdi-1.alphahorizon.io example.pojtinger AAAA
# Test configuration
dig +noall +answer @sdi-1.alphahorizon.io example.pojtinger A
dig +noall +answer @sdi-1.alphahorizon.io www.example.pojtinger CNAME
dig +noall +answer @sdi-1.alphahorizon.io example.pojtinger MX
dig +noall +answer @sdi-1.alphahorizon.io -x 2400:6180:0:d0::1020:c001
dig +noall +answer @sdi-1.alphahorizon.io -x 143.198.37.173
```

## Worker (`sdi-2`)

```shell
ssh jean@sdi-2.alphahorizon.io

sudo apt update
sudo apt install -y bind9 bind9utils
sudo systemctl enable --now named

sudo vi /etc/bind/named.conf.options # Now add the following at the end of the options block:
listen-on port 54 { 127.0.0.1; };
listen-on-v6 port 54 { ::1; };

version "not currently available";
recursion yes;
querylog yes;
allow-transfer { none; };
allow-query { any; };

sudo tee -a /etc/bind/named.conf.local <<'EOT'
zone "example.pojtinger" {
        type slave;
        file "db.example.pojtinger";
        allow-query { any; };
        masters { 143.198.37.173; 2604:a880:cad:d0::d40:1001; };
};

zone "128.199.92.in-addr.arpa" {
        type slave;
        file "db.37.198.143";
        allow-query { any; };
        masters { 143.198.37.173; 2604:a880:cad:d0::d40:1001; };
};

zone "1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2.ip6.arpa" {
        type slave;
        file "db.1.0.0.c.0.2.0.1.0.0.0.0.0.0.0.0.0.d.0.0.0.0.0.0.0.8.1.6.0.0.4.2";
        allow-query { any; };
        masters { 143.198.37.173; 2604:a880:cad:d0::d40:1001; };
};
EOT

sudo named-checkconf
sudo systemctl reload named
```
