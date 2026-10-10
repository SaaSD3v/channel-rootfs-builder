# Ubuntu Rootfs — Channel

Ubuntu Base ARM64 for the Motorola Moto G7 Play.

## Build

The `ubuntu` branch contains `ubuntu/build.sh` and `.github/workflows/ubuntu.yml`.

The workflow installs matching mainline kernel modules. Artifact: `channel-ubuntu-rootfs`.

## Image

Output: `ubuntu-channel-rootfs.ext4.zst` (raw ext4, compressed with zstd). Extract it on the host:

```sh
zstd -d -k ubuntu-channel-rootfs.ext4.zst
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
