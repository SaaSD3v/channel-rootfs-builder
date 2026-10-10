# Debian Rootfs — Channel

Debian Trixie for the Motorola Moto G7 Play.

## Build

The `debian` branch contains `debian/build.sh` and `.github/workflows/debian.yml`.

The workflow can reuse compatible mainline kernel modules or build them as a dependency. Artifact: `channel-debian-rootfs`.

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

## Optional Android sparse tools

The rootfs image is raw ext4. Conversion is optional and does not change the existing boot or deployment process.

Install on Debian:

```sh
sudo apt install android-sdk-libsparse-utils
```

After decompressing the matching `.ext4.zst` file, for example:

```sh
img2simg debian-channel-rootfs.ext4 rootfs-sparse.img
simg2img rootfs-sparse.img rootfs-restored.ext4
```

`img2simg` converts raw to sparse; `simg2img` converts sparse to raw. Do not convert an image that is already sparse.

## Rootfs details

- Artifact: `channel-debian-rootfs`
- Image: `debian-channel-rootfs.ext4.zst`
- Format: ext4 (raw, zstd-compressed)
- Label: `debian`
- UUID: `89530000-6320-4000-8000-000000000001`
