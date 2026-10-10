# Channel Rootfs Builder

ARM64 kernel and rootfs builds for the Motorola Moto G7 Play (`channel`).

## Workflows

**Build mainline kernel** produces `boot-channel.img`, the Channel DTB and matching modules.

**Build Debian rootfs**, **Build Ubuntu rootfs** and **Build Alpine rootfs** produce independent filesystem images. Each workflow builds from its matching distribution branch and may reuse the kernel artifact.

Use the workflow you need from **Actions**.

## Filesystems

Each distribution publishes its own compressed ext4 image: `debian-channel-rootfs.ext4.zst`, `ubuntu-channel-rootfs.ext4.zst` or `alpine-channel-rootfs.ext4.zst`.

The kernel boots directly from the configured root partition. Keep the kernel modules paired with that build.

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

Install on the Linux system handling the image:

| Distribution | Package command |
| --- | --- |
| Debian / Ubuntu | `sudo apt install android-sdk-libsparse-utils` |
| Alpine (community) | `apk add android-tools-img2simg android-tools-simg2img` |

After decompressing the matching `.ext4.zst` file, for example:

```sh
img2simg debian-channel-rootfs.ext4 rootfs-sparse.img
simg2img rootfs-sparse.img rootfs-restored.ext4
```

`img2simg` converts raw to sparse; `simg2img` converts sparse to raw. Do not convert an image that is already sparse.

## Rootfs details

| Distribution | Artifact | Image | Label |
| --- | --- | --- | --- |
| Debian | `channel-debian-rootfs` | `debian-channel-rootfs.ext4.zst` | `debian` |
| Ubuntu | `channel-ubuntu-rootfs` | `ubuntu-channel-rootfs.ext4.zst` | `ubuntu` |
| Alpine | `channel-alpine-rootfs` | `alpine-channel-rootfs.ext4.zst` | `alpine` |

- Format: ext4 (raw, zstd-compressed)
- Ext4 UUID: `89530000-6320-4000-8000-000000000001`
