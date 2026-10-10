# Ubuntu Rootfs — Channel

Ubuntu Base ARM64 for the Motorola Moto G7 Play.

## Build

The `ubuntu` branch contains `ubuntu/build.sh` and `.github/workflows/ubuntu.yml`.

The workflow installs matching mainline kernel modules. Artifact: `channel-ubuntu-rootfs`.

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

## Expand the root filesystem

After boot, run as root and identify the ext4 partition mounted at `/`:

```sh
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINTS
grep ' / ' /proc/mounts
command -v resize2fs
```

If it is missing, run `apt install e2fsprogs`.

Use the **verified root partition** in this command:

```sh
resize2fs /dev/ROOT_PARTITION
df -h /
```

This expands ext4 to the available size of its partition. Never guess the device path.

## Rootfs details

- Artifact: `channel-ubuntu-rootfs`
- Image: `ubuntu-channel-rootfs.ext4.zst`
- Format: ext4 (raw, zstd-compressed)
- Label: `ubuntu`
- UUID: `89530000-6320-4000-8000-000000000001`
