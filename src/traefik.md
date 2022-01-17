# Traefik

```shell
sudo mkdir -p /etc/traefik
sudo tee /etc/traefik/traefik.yaml<<'EOT'
entryPoints:
  dnsTcp:
    address: ":53"

  web:
    address: ":80"

  websecure:
    address: ":443"

  sshalt:
    address: ":2222"

  websecurealt:
    address: ":8443"

  metrics:
    address: "localhost:8082"

providers:
  file:
    filename: /etc/traefik/services.yaml
    watch: true

api:
  dashboard: true

certificatesResolvers:
  letsencrypt:
    acme:
      email: jean@example.com
      storage: /var/lib/traefik/acme.json
      httpChallenge:
        entryPoint: web

log:
  level: INFO

metrics:
  prometheus:
    entryPoint: metrics
EOT

sudo tee /etc/traefik/services.yaml<<'EOT'
udp:
  routers:
    dns:
      entryPoints:
        - dnsUdp
      service: dns
  services:
    dns:
      loadBalancer:
        servers:
          - address: localhost:54

tcp:
  routers:
    dns:
      entryPoints:
        - dnsTcp
      rule: HostSNI(`*`)
      service: dns
    ssh:
      entryPoints:
        - websecurealt
      rule: HostSNI(`*`)
      service: ssh
    giteaSSH:
      entryPoints:
        - sshalt
      rule: HostSNI(`*`)
      service: giteaSSH
    sshOverTLS:
      entryPoints:
        - websecure
      rule: HostSNI(`ssh.jeans-box.example.com`)
      service: ssh
      tls:
        certResolver: letsencrypt
        domains:
          - main: ssh.jeans-box.example.com
    ldap:
      entryPoints:
        - websecure
      rule: HostSNI(`ldap.felixs-sdi1.alphahorizon.io`)
      service: ldap
      tls:
        certResolver: letsencrypt
        domains:
          - main: ldap.felixs-sdi1.alphahorizon.io
  services:
    dns:
      loadBalancer:
        servers:
          - address: localhost:54
    ssh:
      loadBalancer:
        servers:
          - address: localhost:22
    giteaSSH:
      loadBalancer:
        servers:
          - address: localhost:3022
    ldap:
      loadBalancer:
        servers:
          - address: localhost:389

http:
  routers:
    dashboard:
      rule: Host(`traefik.jeans-box.example.com`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: traefik.jeans-box.example.com
      service: api@internal
      entryPoints:
        - websecure
      middlewares:
        - dashboard
    cockpit:
      rule: Host(`cockpit.jeans-box.example.com`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: cockpit.jeans-box.example.com
      service: cockpit
      entryPoints:
        - websecure
    gitea:
      rule: Host(`gitea.jeans-box.example.com`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: gitea.jeans-box.example.com
      service: gitea
      entryPoints:
        - websecure
    dex:
      rule: Host(`dex.jeans-box.example.com`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: dex.jeans-box.example.com
      service: dex
      entryPoints:
        - websecure
    liwasc:
      rule: Host(`liwasc.jeans-box.example.com`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: liwasc.jeans-box.example.com
      service: liwasc
      entryPoints:
        - websecure
    bofied:
      rule: Host(`bofied.jeans-box.example.com`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: bofied.jeans-box.example.com
      service: bofied
      entryPoints:
        - websecure
    apache:
      rule: Host(`apache.felixs-sdi1.alphahorizon.io`) || HostRegexp(`{subdomain:[a-z]+}.apache.felixs-sdi1.alphahorizon.io`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: apache.felixs-sdi1.alphahorizon.io
          - main: marx.apache.felixs-sdi1.alphahorizon.io
          - main: kropotkin.apache.felixs-sdi1.alphahorizon.io
          - main: secure.apache.felixs-sdi1.alphahorizon.io
      service: apache
      entryPoints:
        - websecure
    phpmyadmin:
      rule: Host(`phpmyadmin.felixs-sdi1.alphahorizon.io`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: phpmyadmin.felixs-sdi1.alphahorizon.io
      service: apache
      entryPoints:
        - websecure
    ldapAccountManager:
      rule: Host(`ldap-account-manager.felixs-sdi1.alphahorizon.io`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: ldap-account-manager.felixs-sdi1.alphahorizon.io
      service: apache
      entryPoints:
        - websecure
    nextcloud:
      rule: Host(`nextcloud.felixs-sdi1.alphahorizon.io`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: nextcloud.felixs-sdi1.alphahorizon.io
      service: apache
      entryPoints:
        - websecure
    grafana:
      rule: Host(`grafana.felixs-sdi1.alphahorizon.io`)
      tls:
        certResolver: letsencrypt
        domains:
          - main: grafana.felixs-sdi1.alphahorizon.io
      service: grafana
      entryPoints:
        - websecure

  middlewares:
    dashboard:
      basicauth:
        users:
          - "jean:$apr1$dYdt8Zrl$TsEfzaedPGyjdrDk8EfRN." # htpasswd -nb htpasswd -nb jean asdf

  services:
    cockpit:
      loadBalancer:
        serversTransport: cockpit
        servers:
          - url: https://localhost:9090
    gitea:
      loadBalancer:
        servers:
          - url: http://localhost:3000
    dex:
      loadBalancer:
        servers:
          - url: http://localhost:5556
    liwasc:
      loadBalancer:
        servers:
          - url: http://localhost:15124
    bofied:
      loadBalancer:
        servers:
          - url: http://localhost:15256
    apache:
      loadBalancer:
        servers:
          - url: http://localhost:8080
    grafana:
      loadBalancer:
        servers:
          - url: http://localhost:3000

  serversTransports:
    cockpit:
      insecureSkipVerify: true
EOT

sudo podman run -d --restart=always --label "io.containers.autoupdate=image" --net=host -v /var/lib/traefik/:/var/lib/traefik -v /etc/traefik/:/etc/traefik --name traefik traefik
sudo podman generate systemd --new traefik | sudo tee /lib/systemd/system/traefik.service
sudo systemctl daemon-reload
sudo systemctl enable --now traefik

sudo firewall-cmd --permanent --add-service=dns
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-port=8443/tcp
sudo firewall-cmd --reload

curl -Lu jean:asdf https://traefik.jeans-box.example.com/ # Test the Traefik dashboard
ssh -p 8443 jean@jeans-box.example.com # Test SSH over TCP
ssh -o ProxyCommand="openssl s_client -connect ssh.jeans-box.example.com:443 -quiet" jean # Test SSH over TLS
```
