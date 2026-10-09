# Channel RootFS Builder

Reusable mainline kernel and rootfs builders for the Motorola Moto G7 Play (channel).

- Kernel source: `SaaSD3v/linux`, branch `msm8953/latest`
- Kernel architecture: `arm64`
- Root partition: Android `userdata`
- Root partition PARTUUID used by `boot-channel.img`: `76dbdefa-f243-cd22-5da5-9374e6ad318b`
- Fixed ext4 filesystem UUID used by distro images: `89530000-6320-4000-8000-000000000001`
- Rootfs labels are distro-specific: `debian`, `ubuntu`, `alpine`.

## Boot model

`boot-channel.img` contains the kernel plus the Channel DTB and has no initramfs.
The kernel mounts the existing Android `userdata` partition directly using:

`root=PARTUUID=76dbdefa-f243-cd22-5da5-9374e6ad318b rootfstype=ext4 rootwait rw`

Flashing a distro with `fastboot flash userdata <rootfs.ext4>` replaces the filesystem
inside `userdata` but does not recreate the GPT entry, so the partition PARTUUID stays
the boot locator. The ext4 UUID and distro label belong to the filesystem itself and are
kept separate from the GPT PARTUUID.

## Kernel reuse and rootfs fallback

`build-mainline.yml` is the only workflow that publishes the reusable kernel checkpoint.
By default, Debian, Ubuntu, and Alpine compile the current
`SaaSD3v/linux:msm8953/latest` for each rootfs build. The temporary kernel is
used for matching modules, config and System.map and is not uploaded.

Select `reuse_kernel` explicitly to use a previously published, still-live
`channel-mainline-kernel-*` artifact. You may provide `kernel_run_id` only
when reuse is enabled; leaving it empty uses the latest successful artifact.
If none is available, the workflow builds the kernel locally. Reusing an older
artifact is an intentional opt-in; the reused kernel commit is shown in the logs.

The distro workflow files are mirrored on the default branch only so GitHub exposes their
manual **Run workflow** controls. Their SSH inputs belong to the rootfs workflows; the
`Build mainline kernel` workflow itself has no SSH inputs and builds no userspace.

## Manual GitHub Actions interface

- Choose `Build Debian/Ubuntu/Alpine rootfs (USB open root)` for direct root SSH over USB with **no key/password fields**. The only option is `reuse_kernel`, disabled by default.
- Choose the regular `Build ... rootfs` workflow for key/password modes. The `ssh_public_key` input is only applicable to `public-key-input` and `public-key-input+password-secret`. Nonempty keys in other modes are rejected.
- The native GitHub `workflow_dispatch` form cannot hide inputs dynamically. We use separate USB workflows to avoid showing unused fields; rootfs workflows are reusable with `workflow_call`.
- The default `main` branch exposes launchers. The actual build scripts and overlays are checked out from the corresponding distribution branch.
