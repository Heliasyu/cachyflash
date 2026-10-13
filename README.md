# CachyFlash

A Linux app that downloads a CachyOS installer and writes it to a USB stick.

**Pre-release 1.3.** [Download the AppImage](https://github.com/Heliasyu/cachyflash/releases/tag/v1.3).

The same notes are on the [documentation page](https://heliasyu.github.io/cachyflash/).

## Quick start

1. Download `CachyFlash-1.3-x86_64.AppImage`.
2. Make it executable and open it. No arguments opens the window.

```sh
chmod +x CachyFlash-1.3-x86_64.AppImage
./CachyFlash-1.3-x86_64.AppImage
```

3. Download the latest image, or choose an ISO you already have. Wait until the bar finishes.
4. Press **Detect device**. The insert dialog opens right away. Plug the stick in once after that.
5. Confirm the drive, confirm the erase warning, then approve the password popup.

A stick that was already plugged in is ignored until you unplug it and plug it in again.

## Requirements

- x86_64 Linux
- Qt 6 for the window. CachyOS already has it.
- polkit (`pkexec`) or `sudo` for the password popup
- A USB stick. 8 GB is the comfortable size. 4 GB is the minimum for the desktop image.

## Images

Checked 12 Oct 2026. A newer ISO works from **Choose an ISO**, or from `--iso` with `--sha256`.

| Image | File | Size | SHA256 |
| --- | --- | --- | --- |
| Desktop 260809 | `cachyos-desktop-linux-260809.iso` | 2.97 GiB | `959f6577f45e25ee9fd8c220fd221b08e4ea79412c7315c0f922dd6d86d5e33c` |
| Handheld 260628 | `cachyos-handheld-linux-260628.iso` | 2.48 GiB | `0567c61f21622e5b4b482a874c41076beeb9bd8c00f046fa40e5c8cfbe34d15f` |

Desktop is the online installer. You pick the desktop during install. Handheld is the game-mode image for Steam Deck, ROG Ally, Legion Go, and Legion Go S.

Downloads are kept in `~/.cache/cachyflash/`. Writing the stick does not delete that file.

## Command line

Arguments skip the window.

```sh
./CachyFlash-1.3-x86_64.AppImage --help
./CachyFlash-1.3-x86_64.AppImage --edition desktop
./CachyFlash-1.3-x86_64.AppImage --edition handheld --gpg
./CachyFlash-1.3-x86_64.AppImage --dry-run --edition desktop
```

`--dry-run` writes nothing. `--gpg` checks CachyOS key `F3B607488DB35A47`. Mirrors, in order: `mirror.cachyos.org`, then `us.cachyos.org`.

## Safety

- The target is the whole disk, such as `/dev/sdb`, never a partition.
- The disk that holds `/`, `/boot`, `/efi`, `/home`, or `/usr` is refused.
- Disks that are not USB or removable are refused.
- A download with the wrong SHA256 is deleted from the cache. Nothing is written.
- The write is `dd bs=4M status=progress oflag=sync`, then a check of the first 1 MiB.

After it finishes, reboot, open the firmware boot menu, and choose the stick. Turn Secure Boot off if the stick is missing. The installer needs internet.
