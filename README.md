# Alpine Rootfs — Channel

Alpine Linux ARM64 with OpenRC for the Motorola Moto G7 Play.

## Build

The `alpine` branch contains `alpine/build.sh` and `.github/workflows/alpine.yml`.

The workflow installs matching mainline kernel modules. Artifact: `channel-alpine-rootfs`.

## Image

Output: `alpine-channel-rootfs.ext4.zst` (raw ext4, compressed with zstd). Extract it on the host:

```sh
zstd -d -k alpine-channel-rootfs.ext4.zst
```

Keep using your established Channel boot setup. This build does not deploy or flash the image.

After boot, `df -h /` shows the available space. To grow ext4 into unused space on the existing root partition, first verify its device using `findmnt -n -o SOURCE,FSTYPE /`. Use `resize2fs` only with that confirmed ext4 partition.

## Network

Connect over the USB gadget:

```sh
ssh root@172.16.42.1
```

For Wi-Fi, use NetworkManager on the device:

```sh
nmcli device wifi list
nmcli --ask device wifi connect "SSID" ifname wlan0
```

## Time

If the clock is incorrect, set the actual UTC time manually:

```sh
date -u -s "YYYY-MM-DD HH:MM:SS"
date
```
