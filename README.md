<!--
# SPDX-FileCopyrightText: Copyright 2025 Eden Emulator Project
# SPDX-License-Identifier: GPL-3.0-or-later

# SPDX-FileCopyrightText: 2018 yuzu Emulator Project
# SPDX-License-Identifier: GPL-2.0-or-later
-->

<p align="center">
  <img src="dist/branding/lichii_icon_with_logo.png" alt="Lichii" width="600">
</p>

# Lichii

Lichii is an experimental, open-source Nintendo Switch and Switch 2 emulator written in C++. It builds on Eglantine, Eden, yuzu and Sudachi, preserving their contributions while developing this fork's game compatibility and renderer fixes.

This is an independent fork, not an official release of those upstream projects. Lichii is not affiliated with Nintendo.

## Deck optimizations branch

The `deck-optimizations` branch adds four commits to [zortos293/Lichii](https://github.com/zortos293/Lichii) `nx2`. They make The Legend of Zelda: Ocarina of Time (0400486024DEC000) playable on GPUs that are too slow to emulate its upscaling network, such as the Steam Deck.

The game runs its upscaler as a tensor-core network. Lichii emulates that network in software, which costs about 127 ms per frame on a Steam Deck. With these commits, a network program is rejected unless "Use the game's own DLSS" is on. A bilinear copy of the network's input to its output takes its place, as upstream Eglantine does for other games.

| Commit | Change |
|---|---|
| `video_core: let a 2D blit read its source as a format the 2D engine lacks` | Port of Eglantine PR #614. The copy can read the E5B9G9R9 gameplay frame. |
| `shader: let the Ampere lowering reject matrix multiplies` | The shader lowering can reject a matrix multiply (HMMA) in place of emulating it. |
| `video_core: stand in for tensor programs unless the game's own DLSS is on` | Network programs are rejected and the bilinear copy draws in their place. Donkey Kong Bananza and Fire Emblem: Fortune's Weave still emulate their networks. |
| `video_core: upscale stand-in copies a network that keeps the frame's size` | Ocarina of Time's gameplay and video networks keep the 1920x1080 size. Without this change, gameplay and videos are black. |

Results on a Steam Deck (LCD, 15 W, docked at 1920x1080, Linux build):

| Scene | `nx2` | This branch |
|---|---|---|
| Title screen | 7.6 fps | 56.6 fps |
| Gameplay, GPU mode Accurate (default) | Not measured | 6.5 fps |
| Gameplay, GPU mode Fast | Not measured | 14.4 fps |

The game's own network gives a sharper image than the bilinear copy. To use it, turn on "Use the game's own DLSS" in the graphics settings.

For better gameplay speed, set the GPU mode to Fast for this game only:

1. Right-click the game and select "Configure Game".
2. On the "Adv. Graphics" tab, set "GPU Mode" to "Fast".

On the Deck, this change doubles the gameplay frame rate.

## Downloads and setup

Published builds belong on this fork's [releases page](https://github.com/zortos293/Eglantine/releases). The repository still uses its historical Eglantine name; the desktop application is now Lichii.

On Windows, extract the entire build archive and run **lichii.exe**. Only use games and system files you have legally obtained. Games, keys and firmware are not included.

### Existing saves and settings

The rebrand intentionally retains the existing Eden/Eglantine data directories and configuration keys. It does not move or delete saves, settings or shader caches. Back up your saves before testing experimental builds.

## Compatibility and issue reports

Compatibility and performance vary by game, hardware and driver. Rendering errors, stutters and crashes remain possible. Passing automated tests does not establish that a game renders correctly, and this project makes no universal frame-rate guarantee.

When [reporting an issue](https://github.com/zortos293/Eglantine/issues), include:

- The exact build, game version, CPU, GPU and driver version.
- Relevant settings and steps to reproduce the problem.
- A screenshot and the log from the affected session, after checking it for personal information.

Do not upload game files, keys, firmware or private account information.

## Building and contributing

See the [build guide](docs/Build.md), [user documentation](docs/user/README.md) and [coding guidelines](docs/Coding.md). Some inherited documentation and internal build target names still use upstream names. The GUI build target remains `yuzu`; its output is `lichii.exe` on Windows and `lichii` on other desktop platforms.

Keep changes focused and distinguish code-level checks from actual gameplay testing.

## Artwork
Art created by YOB (yobslab)
The supplied Lichii logo, icon and library artwork are in [dist/branding](dist/branding). Windows, Linux and macOS icon resources are generated from that artwork by [Generate-LichiiIcons.ps1](tools/Generate-LichiiIcons.ps1).

## Support Lichii

Lichii is free and stays free. If you want to help, you can donate in crypto. Donations help pay for the AI tools and build machines used to develop this open-source project faster.

**Solana (SOL)**
```text
JepEjfyWjGjhJvHSrqj52KtLjcbbbmUh1iGqerYBF4w
```

**Ethereum (ETH)**
```text
0x055ABa1b80Bc35C836Ce7E9F47c2b3d6C75b5534
```

**Bitcoin (BTC)**
```text
bc1qcvxdpf4qvdhqp8ee4s355cjmxrzsphsdwpl8w4
```

**BNB Smart Chain (BNB)**
```text
0x055ABa1b80Bc35C836Ce7E9F47c2b3d6C75b5534
```

**Polygon**
```text
0x055ABa1b80Bc35C836Ce7E9F47c2b3d6C75b5534
```

Send each coin on its matching network. Coins sent on the wrong network can't be recovered.

> [!NOTE]
> Donations are voluntary gifts and are non-refundable. A donation does not buy any product, service, perk, early access, exclusive build, priority support or say in the project. Every release stays free and public for everyone.

## Credits and license

Lichii inherits the work of the Eglantine, Eden, yuzu and Sudachi projects and their contributors, including Eglantine's original developer Camille LaVey. Original copyright notices and third-party licenses are retained.

See [LICENSE.txt](LICENSE.txt) and the source files for applicable license terms.
