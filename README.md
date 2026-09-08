# AviumUI for OnePlus 9R personal build

<!-- > 📜 [Changelog](./docs/changelog.md) · [Flashing](./docs/flashing.md) -->

This is the local manifest for my [AviumUI](https://aviumui.org/) for [OnePlus 9R](https://www.oneplus.com/cn/9r/specs) personal build.

## Fork info

- Upstream manifest branch: `avium-16.2`
- Personally forked projects branch: `avium-16.2-pb`
- Local manifest: [roomservice.xml](./local_manifests/roomservice-20260816.xml)

## Published builds info

| Version         | Upstream sync date          | Manifest                                               | Snapshot                                               |
| --------------- | --------------------------- | ------------------------------------------------------ | ------------------------------------------------------ |
| 16.2.1-20260904 | `2026-09-08 06:34:32+00:00` | [20260816](./local_manifests/roomservice-20260816.xml) | [20260908](./snapshot_manifests/snapshot-20260908.xml) |
| 16.2.1-20260825 | `2026-08-25 14:36:11+00:00` | [20260816](./local_manifests/roomservice-20260816.xml) | [20260825](./snapshot_manifests/snapshot-20260825.xml) |

Snapshots are manifest files that are created with command `repo manifest -r -o snapshot.xml`. They could possibly be used to reproduce\* a certain historical build (see below), as long as the commits referenced by them are properly preserved.

<details>
<summary>Older builds without snapshot manifests</summary>

Trying to reproduce these older builds is considered not very feasible since the repositories have undergone extensive rebases after 2026-06-15.

| Version         | Cutoff date                 | Manifest                                               |
| --------------- | --------------------------- | ------------------------------------------------------ |
| 16.2.1-20260615 | `2026-06-15 08:11:56+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |
| 16.2.1-20260614 | `2026-06-14 08:49:35+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |
| 16.2.1-20260609 | `2026-06-09 07:07:13+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |
| 16.2.1-20260607 | `2026-06-07 01:49:42+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |
| 16.2.1-20260603 | `2026-06-03 14:40:14+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |
| 16.2.1-20260522 | `2026-05-22 15:48:52+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |
| 16.2.1-20260518 | `2026-05-18 12:20:03+00:00` | [20260516](./local_manifests/roomservice-20260516.xml) |

</details>

\*) The AOSP build system is not designed to produce bit-to-bit identical builds, so by "reproduce", we just mean recompiling a specific version from source, not reproducing the identical artifacts.

## Build instructions

1. Visit LineageOS official build guide: [Build for lemonades | LineageOS Wiki](https://wiki.lineageos.org/devices/lemonades/build/). Confirm system requirements and configure a Ubuntu 24.02 environment according to this guide, and stop right before the `repo init` step.
   - Building Android 16 requires ~48GB available RAM, but you can make use of ZRAM or Swap space to meet this requirement.
   - It is recommended to have 400GB of storage space, but actually it requires ~210GB for the checked-out source tree and ~170GB for the build output. If you do not have 400GB of storage space on one drive, you can first sync source tree into one drive and redirect build output into another by using symlink (`ln -s /path/to/desired/build/output/dir/ ./out`).
   - You need to install `erofs-utils` (`sudo apt install erofs-utils`) for blob extraction. This is not mentioned in the guide.

2. Visit AviumUI build instructions: [AviumUI/android_manifests](https://github.com/AviumUI/android_manifests/blob/avium-16.2/README.mkdn). Perform `repo init` according to it.

3. Create directory `.repo/local_manifests`. Rename the roomservice manifest provided in this repo to `roomservice.xml` and then drop it into the `local_manifests` folder.

4. If you want to sync from a Snapshot Manifest, place the manifest file in `.repo/manifests` and then run `repo init -m snapshot-<date>.xml` to reinitialize with the snapshot.
   - Skip this step if you want a latest sync.

5. Perform `repo sync` according to AviumUI build instructions.
   - For Chinese builders: Consider [using mirrors for android.googlesource.com](https://help.mirrors.cernet.edu.cn/AOSP/) to speed up synchronization and avoid wasting too much overseas traffic quota. The `git config --global` way is recommended since it applies the mirrors transparently without altering the git repositories or the manifests.
   - You may not want to use `-j$(nproc --all)` when syncing. Use `-j4` (4 concurrent actions) is recommended. Note that some mirrors might ban you if you use more than `-j4`.

6. Perform `breakfast` and blob extraction steps according to LineageOS build guide. It is recommended to extract blobs from [Neokoni's latest build](https://ota.neokoni.ink/device/lemonades/AviumUI/avium-16) or a personal build published by me.
   - Using stock ROM is not very recommended since the sm8250-common blobs are extracted from various different sources. You need to manually mix different stock ROM packages to obtain a complete dump.

7. Perform `lunch` and `m bacon` according to AviumUI build instructions.
   - You may not want to use `-j$(nproc --all)`. Overly high job count may lag other applications or cause OOM.

8. Locate build outputs and flash according to LineageOS build guide and [flashing guide](https://wiki.lineageos.org/devices/lemonades/install/).
   - Quick upgrading sequence once installed: `adb reboot sideload-auto-reboot; adb wait-for-sideload && adb sideload lineage_lemonades-ota.zip`.
