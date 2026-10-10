# Alpine Rootfs — Channel

Alpine Linux ARM64 with OpenRC for the Motorola Moto G7 Play.

## Build

The `alpine` branch contains `alpine/build.sh` and `.github/workflows/alpine.yml`.

The workflow installs matching mainline kernel modules. Artifact: `channel-alpine-rootfs`.

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

## Alpine utilities

If `findmnt` is unavailable, use `grep ' / ' /proc/mounts` or `df -h /`.

Optional packages:

```sh
apk add e2fsprogs-extra          # resize2fs
apk add android-tools-img2simg  # Android sparse tool (community)
```

The output is raw ext4; the build does not need sparse conversion.

## Rootfs details

- Artifact: `channel-alpine-rootfs`
- Image: `alpine-channel-rootfs.ext4.zst`
- Format: ext4 (raw, zstd-compressed)
- Label: `alpine`
- UUID: `89530000-6320-4000-8000-000000000001`
