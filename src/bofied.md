# bofied

```shell
sudo mkdir -p /var/lib/bofied
sudo podman run -d --restart=always --label "io.containers.autoupdate=image" --net host --cap-add NET_BIND_SERVICE -v /var/lib/bofied:/root/.local/share/bofied -e BOFIED_BACKEND_OIDCISSUER=https://dex.jeans-box.example.com -e BOFIED_BACKEND_OIDCCLIENTID=bofied -e BOFIED_BACKEND_ADVERTISEDIP=100.64.154.249 --name bofied pojntfx/bofied-backend
sudo podman generate systemd --new bofied | sudo tee /lib/systemd/system/bofied.service
sudo systemctl daemon-reload
sudo systemctl enable --now bofied
sudo firewall-cmd --permanent --add-port=67/udp
sudo firewall-cmd --permanent --add-port=69/udp
sudo firewall-cmd --permanent --add-port=4011/udp
sudo firewall-cmd --reload
```

Now visit [https://pojntfx.github.io/bofied/](https://pojntfx.github.io/bofied/) and login using the following credentials:

- Backend URL: `https://bofied.jeans-box.example.com/`
- OIDC Issuer: `https://dex.jeans-box.example.com`
- OIDC Client ID: `bofied`
- OIDC Redirect URL: `https://pojntfx.github.io/bofied/`
