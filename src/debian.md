# Debian

```shell
sudo umount /dev/mmcblk0{,p1,p0}
curl -L 'https://raspi.debian.net/tested/20210823_raspi_3_bullseye.img.xz' | xzcat >/tmp/debian.img
sudo dd if=/tmp/debian.img of=/dev/mmcblk0 bs=4M status=progress
sync

sudo mkdir -p /mnt/raspi-boot
sudo mount /dev/mmcblk0p1 /mnt/raspi-boot
{
    echo "root_pw=$(openssl rand -base64 12)"
    echo "root_authorized_key=$(cat ~/.ssh/id_rsa.pub)"
    echo "hostname=jeans-box"
} >>/mnt/raspi-boot/sysconf.txt
sudo umount /dev/mmcblk0{,p1,p0}
```
