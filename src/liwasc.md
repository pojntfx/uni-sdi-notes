# liwasc

```shell
sudo mkdir -p /var/lib/liwasc
sudo podman run -d --restart=always --label "io.containers.autoupdate=image" --net host --cap-add NET_RAW --ulimit nofile=16384:16384 -v /var/lib/liwasc:/root/.local/share/liwasc -e LIWASC_BACKEND_OIDCISSUER=https://dex.jeans-box.example.com -e LIWASC_BACKEND_OIDCCLIENTID=liwasc -e LIWASC_BACKEND_DEVICENAME=eth0 -e LIWASC_BACKEND_PERIODICSCANCRONEXPRESSION='0 0 * * *' --name liwasc pojntfx/liwasc-backend
sudo podman generate systemd --new liwasc | sudo tee /lib/systemd/system/liwasc.service
sudo systemctl daemon-reload
sudo systemctl enable --now liwasc
```

Now visit [https://pojntfx.github.io/liwasc/](https://pojntfx.github.io/liwasc/) as we did before and use `wss://liwasc.jeans-box.example.com/` as the backend URL (note the trailing slash!).
