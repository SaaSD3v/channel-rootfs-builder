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

## Rootfs details

| Distribution | Artifact | Image | Label |
| --- | --- | --- | --- |
| Debian | `channel-debian-rootfs` | `debian-channel-rootfs.ext4.zst` | `debian` |
| Ubuntu | `channel-ubuntu-rootfs` | `ubuntu-channel-rootfs.ext4.zst` | `ubuntu` |
| Alpine | `channel-alpine-rootfs` | `alpine-channel-rootfs.ext4.zst` | `alpine` |

- Format: ext4 (raw, zstd-compressed)
- Ext4 UUID: `89530000-6320-4000-8000-000000000001`
