# Channel RootFS Builder

Reusable mainline kernel and rootfs builders for the Motorola Moto G7 Play (channel).

- Kernel source: `SaaSD3v/linux`, branch `msm8953/latest`
- Kernel architecture: `arm64`
- Fixed root filesystem UUID: `89530000-6320-4000-8000-000000000001`
- Rootfs labels are distro-specific (for example `debian`).

## Current workflow behavior

The `debian` rootfs workflow compiles the latest kernel from
`SaaSD3v/linux:msm8953/latest` by default. Set `reuse_kernel=true` to
explicitly reuse a published kernel artifact; only then may you enter
`kernel_run_id` to choose a particular run. The chosen kernel commit appears
in the workflow log. A missing reusable artifact falls back to a temporary
kernel build.

For direct root SSH over USB without key/password inputs, use
`Build Debian rootfs (USB open root)` on the
`main` Actions page. Use the ordinary rootfs workflow for authenticated SSH.
The USB workflow is separate because native GitHub Actions forms cannot hide
fields after a choice changes.
