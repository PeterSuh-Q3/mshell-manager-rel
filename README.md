<!-- @format -->

# MSHELL Manager

[![GitHub release](https://img.shields.io/github/release/PeterSuh-Q3/mshell-manager?include_prereleases=&sort=semver&color=blue)](https://github.com/PeterSuh-Q3/mshell-manager/releases/)
[![License](https://img.shields.io/badge/License-MIT-blue)](#license)
[![issues - mshell-manager](https://img.shields.io/github/issues/PeterSuh-Q3/mshell-manager)](https://github.com/PeterSuh-Q3/mshell-manager/issues)

A DSM package to configure the [MSHELL](https://github.com/PeterSuh-Q3/tinycore-redpill)
(`alpine-redpill` branch) loader, monitor hardware, and reboot into a
different loader mode - all from inside DSM, without ever dropping
into the TinyCore boot menu.

## ✨ Features

| Tab | What it does |
|---|---|
| 🧭 **System Info** | DSM/loader/package version cards, live CPU clock/temperature, disk (Storage Controllers) temp + SMART health, network interface status |
| 🔩 **Configuration** | Edit loader settings (serial number, MAC addresses, kernel/network parameters) that live in `user_config.json` on the loader disk; reboot into Rebuild Mode or DSM Reinstall |
| 🗄️ **Storage Panel** | Change the disk-bay layout DSM's own Storage Manager displays - **applies live**, no loader rebuild needed |
| 🎞️ **VCRT** | Inspect and (re)link the hardware video transcoding runtime / VCRT requires the installation of Sino Community FFMPEG 8.1 to be activated |
| 🎮 **NVIDIA** | Live NVIDIA GPU status via `nvidia-smi` |
| 🕹️ **AMD GPU** | Live AMD GPU status and console output |
| 🔍 **Terminal & dmesg** | Kernel log viewer and an in-browser terminal (via `ttyd`) |

## 📸 Screenshots

<table>
<tr>
<td><img src="captures/mshell-manager-1.0.0/01-system-info.png" alt="System Info" width="400"></td>
<td><img src="captures/mshell-manager-1.0.0/02-configuration-masked.png" alt="Configuration" width="400"></td>
</tr>
<tr>
<td><img src="captures/mshell-manager-1.0.0/03-storage-panel.png" alt="Storage Panel" width="400"></td>
<td><img src="captures/mshell-manager-1.0.0/04-vcrt.png" alt="VCRT" width="400"></td>
</tr>
<tr>
<td><img src="captures/mshell-manager-1.0.0/05-nvidia.png" alt="NVIDIA" width="400"></td>
<td><img src="captures/mshell-manager-1.0.0/06-terminal-dmesg.png" alt="Terminal & dmesg" width="400"></td>
</tr>
</table>

## 📦 Installation

Download the latest `.spk` from the [Releases page](https://github.com/PeterSuh-Q3/mshell-manager/releases)
and install it manually via **Package Center → Manual Install**.

Requires DSM 7.0 or later, booted via the MSHELL (tinycore-redpill)
loader.

## 🔐 Privilege model

- The web UI (`api.cgi`) runs as a non-root service account, declared
  via `conf/privilege`'s `defaults` - no elevated privileges by
  default.
- `mshell-helper` (setuid root, mode `6550`) is the *only* root entry
  point. It whitelists a fixed set of actions and, if the request is
  valid, `execv()`s the real backend script directly - it never runs
  an interpreter shell itself.
- The backend script (`bin/mshell-backend.sh`) is **also** provisioned
  by DSM via `conf/privilege`'s `tool` section, owned `root:<pkg-group>`
  mode `0550` - the non-root service account can execute it (through
  the setuid helper) but can never modify it. That closes a real gap:
  if the backend logic lived in a file the service account could
  edit, that account could rewrite it and have the setuid helper
  execute arbitrary code as root.

Both `tool` entries are provisioned by DSM itself at install time -
`postinst` never needs root.

## 🏗️ Architecture

- [`docs/mshell-config-architecture.md`](docs/mshell-config-architecture.md) -
  where `user_config.json` actually lives on disk, how the loader disk
  is detected, the full backend action inventory, and several gotchas
  found the hard way on real hardware (a frontend-bundle file that
  looks live but isn't, an `nginx.conf` filename requirement, a
  state-merge bug from moving a tab's cards, and more).
- [`docs/synology-spk-build-notes.md`](docs/synology-spk-build-notes.md) -
  general DSM SPK packaging pitfalls (privilege model, INFO fields,
  builder gotchas).

## 🙏 Credits

- [`PeterSuh-Q3/tinycore-redpill`](https://github.com/PeterSuh-Q3/tinycore-redpill) -
  the MSHELL loader this package configures.
- Storage Panel's live-patch logic is ported from
  [Change Panel Size](https://github.com/wjz304)'s `storagepanel.sh`
  (Copyright © 2022 Ing, MIT License).

## License

This repository is licensed under the [MIT License](LICENSE).

This work is not affiliated with Synology Inc. in any way. It is an
independent project. It is not an official Synology product and does
not have any official support from Synology Inc. Use at your own risk.

---

<details>
<summary>🇰🇷 한국어 설명 (클릭하여 펼치기)</summary>

# MSHELL Manager

[MSHELL](https://github.com/PeterSuh-Q3/tinycore-redpill)(`alpine-redpill`
브랜치) 로더를 DSM 안에서 바로 설정하고, 하드웨어 상태를 모니터링하고,
다른 로더 모드로 재부팅할 수 있게 해주는 DSM 패키지입니다. TinyCore
부팅 메뉴로 따로 진입할 필요가 없습니다.

## ✨ 기능

| 탭 | 설명 |
|---|---|
| 🧭 **System Info** | DSM/로더/패키지 버전 카드, 실시간 CPU 클럭/온도, 디스크(Storage Controllers) 온도·SMART 상태, 네트워크 인터페이스 상태 |
| 🔩 **Configuration** | 로더 디스크의 `user_config.json`에 저장되는 로더 설정(시리얼 번호, MAC 주소, 커널/네트워크 파라미터) 편집, Rebuild Mode·DSM Reinstall로 재부팅 |
| 🗄️ **Storage Panel** | DSM 자체 Storage Manager가 표시하는 디스크 베이 레이아웃 변경 - **즉시 반영**, 로더 재빌드 불필요 |
| 🎞️ **VCRT** | 하드웨어 영상 트랜스코딩 런타임 상태 확인 및 재연결 / VCRT 는 시노커뮤니티 FFMPEG 8.1 을 설치해야 활성화 |
| 🎮 **NVIDIA** | `nvidia-smi` 기반 실시간 NVIDIA GPU 상태 |
| 🕹️ **AMD GPU** | 실시간 AMD GPU 상태 및 콘솔 출력 |
| 🔍 **Terminal & dmesg** | 커널 로그 뷰어 및 브라우저 내 터미널(`ttyd`) |

## 📸 스크린샷

<table>
<tr>
<td><img src="captures/mshell-manager-1.0.0/01-system-info.png" alt="System Info" width="400"></td>
<td><img src="captures/mshell-manager-1.0.0/02-configuration-masked.png" alt="Configuration" width="400"></td>
</tr>
<tr>
<td><img src="captures/mshell-manager-1.0.0/03-storage-panel.png" alt="Storage Panel" width="400"></td>
<td><img src="captures/mshell-manager-1.0.0/04-vcrt.png" alt="VCRT" width="400"></td>
</tr>
<tr>
<td><img src="captures/mshell-manager-1.0.0/05-nvidia.png" alt="NVIDIA" width="400"></td>
<td><img src="captures/mshell-manager-1.0.0/06-terminal-dmesg.png" alt="Terminal & dmesg" width="400"></td>
</tr>
</table>

## 📦 설치

[릴리즈 페이지](https://github.com/PeterSuh-Q3/mshell-manager/releases)에서
최신 `.spk`를 받아 **패키지 센터 → 수동 설치**로 설치하세요.

DSM 7.0 이상, MSHELL(tinycore-redpill) 로더로 부팅된 환경이 필요합니다.

## 🔐 권한 모델

- 웹 UI(`api.cgi`)는 `conf/privilege`의 `defaults`로 선언된 **권한
  없는 서비스 계정**으로 실행됩니다 — 기본적으로 권한 승격이 없습니다.
- `mshell-helper`(setuid root, 모드 `6550`)가 **유일한 root 진입점**입니다.
  고정된 화이트리스트의 액션만 허용하고, 유효한 요청이면 셸 인터프리터를
  거치지 않고 바로 `execv()`로 실제 백엔드 스크립트를 실행합니다.
- 백엔드 스크립트(`bin/mshell-backend.sh`) 역시 `conf/privilege`의
  `tool` 섹션을 통해 DSM이 직접 `root:<패키지그룹>` 모드 `0550`로
  배치합니다 — 비root 서비스 계정은 (setuid 헬퍼를 통해) 실행만
  가능하고 절대 수정할 수 없습니다. 백엔드 로직이 서비스 계정이
  쓸 수 있는 파일에 있었다면, 그 계정이 파일을 고쳐서 setuid
  헬퍼가 root로 임의 코드를 실행하게 만들 수 있었을 것입니다 —
  이 구조로 그 허점을 막았습니다.

두 `tool` 항목 모두 설치 시점에 DSM 자신이 부여하므로, `postinst`는
한 번도 root 권한이 필요하지 않습니다.

## 🏗️ 아키텍처

- [`docs/mshell-config-architecture.md`](docs/mshell-config-architecture.md) —
  `user_config.json`이 디스크 어디에 실제로 있는지, 로더 디스크를
  어떻게 탐지하는지, 백엔드 액션 전체 목록, 그리고 실기에서 직접
  겪으며 알아낸 여러 함정들(겉보기엔 반영되는 것 같지만 실제로는
  안 쓰이는 프론트엔드 번들 파일, `nginx.conf`의 파일명 요구사항,
  탭 이동 중 발생한 상태 병합 버그 등)을 정리했습니다.
- [`docs/synology-spk-build-notes.md`](docs/synology-spk-build-notes.md) —
  DSM SPK 패키징 전반의 함정(권한 모델, INFO 필드, 빌더 관련 주의사항).

## 🙏 크레딧

- [`PeterSuh-Q3/tinycore-redpill`](https://github.com/PeterSuh-Q3/tinycore-redpill) —
  이 패키지가 설정하는 MSHELL 로더 본체.
- Storage Panel의 즉시 반영 로직은 [Change Panel Size](https://github.com/wjz304)의
  `storagepanel.sh`(Copyright © 2022 Ing, MIT License)를 이식했습니다.

## 라이선스

이 저장소는 [MIT License](LICENSE)를 따릅니다.

이 프로젝트는 Synology Inc.와 어떠한 관계도 없는 독립적인 프로젝트이며,
공식 Synology 제품이 아니고 Synology Inc.의 공식 지원을 받지 않습니다.
사용에 따른 책임은 사용자 본인에게 있습니다.

</details>
