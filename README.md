# MSHELL Manager — public release mirror

This repository holds no source code. It exists solely so loader builds
(`tinycore-redpill`) can fetch the latest MSHELL Manager `.spk` and its
verified checksum from a **public** GitHub Releases API endpoint, the same
way `syno-amdgpu-driver` is consumed.

MSHELL Manager's actual source lives in a private repository
(`PeterSuh-Q3/mshell-manager`). Its release workflow publishes here
automatically whenever a new version is cut — this repo is not updated
manually.
