# CachyFlash

Linux tool that downloads a CachyOS installer and writes it to a USB stick. Version 1.3.

The AppImage opens a window. Pass arguments and the same file runs the command-line writer instead.

Documentation site: https://heliasyu.github.io/cachyflash/

## Requirements

- x86_64 Linux
- Qt 6, for the window. CachyOS already has it.
- polkit (`pkexec`) or `sudo`, for the password popup
- `lsblk`, `sha256sum`, and `dd`
- A USB stick of at least 4 GB

## Open the window

```sh
chmod +x CachyFlash-1.3-x86_64.AppImage
./CachyFlash-1.3-x86_64.AppImage
```

No arguments means the window. The download of the AppImage is the file shipped with the CachyFlash page.

## Window

Choose the image before Detect device. The write button stays off until a file is ready.

| Button | Meaning |
| --- | --- |
| Image required | No file yet. The button cannot be pressed. |
| Preparing image | Download or SHA256 check is running. |
| Detect device | The image is ready. Press this before you insert the stick. |
| Waiting for device | The insert dialog has been confirmed. Plug the stick in once. |
| Writing image | Do not remove the stick. |

Detect device opens the insert dialog immediately. Plug the stick in after that dialog. A stick that was already inserted is ignored until you remove it and plug it in again.

Confirm the detected disk, then confirm the erase warning. The password request is a system popup.

## Downloaded image

Download latest saves the ISO under `~/.cache/cachyflash/`. Writing the stick copies that file. It does not delete it. The next Download latest reuses the file when the size and SHA256 still match.

Choose an ISO leaves your original file where you picked it. An image that is not a pinned release asks before it is used.

## What the write does

The stick is the whole disk, not a partition. That is the hybrid layout the installer boots from.

```sh
dd bs=4M status=progress oflag=sync
```

After the write it syncs the disk and compares the first 1 MiB with the image.

## What it refuses

- The disk that holds `/`, `/boot`, `/efi`, `/home`, or `/usr`
- Partitions. The target is the disk itself, such as `/dev/sdb`
- Disks that are not USB or removable
- A stick smaller than the image
- A download whose SHA256 does not match the pinned value

## Boot the stick

1. Reboot and open the firmware boot menu.
2. Choose the stick. If it is missing, turn Secure Boot off for the install.
3. The installer needs a working internet connection.

Desktop 260809 lets you pick the desktop during install. Handheld 260628 is the game-mode image for Steam Deck, ROG Ally, Legion Go, and Legion Go S.

## Command line

```sh
./CachyFlash-1.3-x86_64.AppImage --help
./CachyFlash-1.3-x86_64.AppImage --edition desktop
./CachyFlash-1.3-x86_64.AppImage --edition handheld --gpg
./CachyFlash-1.3-x86_64.AppImage --edition desktop --device /dev/sdb --yes
./CachyFlash-1.3-x86_64.AppImage --iso PATH --sha256 HEX --device /dev/sdb
./CachyFlash-1.3-x86_64.AppImage --dry-run --edition desktop
```

`--dry-run` writes nothing. `--gpg` checks the CachyOS key `F3B607488DB35A47` (fingerprint `882D CFE4 8E20 51D4 8E25 62AB F3B6 0748 8DB3 5A47`).

Mirrors, in order: `https://mirror.cachyos.org`, then `https://us.cachyos.org`.

The script in this repository is the command-line writer. The AppImage calls it when you pass arguments.

## Pinned images

Checked 12 Oct 2026.

| Image | File | Size | SHA256 |
| --- | --- | --- | --- |
| Desktop 260809 | `cachyos-desktop-linux-260809.iso` | 2.97 GiB | `959f6577f45e25ee9fd8c220fd221b08e4ea79412c7315c0f922dd6d86d5e33c` |
| Handheld 260628 | `cachyos-handheld-linux-260628.iso` | 2.48 GiB | `0567c61f21622e5b4b482a874c41076beeb9bd8c00f046fa40e5c8cfbe34d15f` |

A newer ISO works with Choose an ISO, or with `--iso` and `--sha256`.
