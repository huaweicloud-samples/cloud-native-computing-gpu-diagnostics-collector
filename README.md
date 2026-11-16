# cloud-native-computing-gpu-diagnostics-collector / GPU Diagnostics Collector

[![License](https://img.shields.io/badge/License-MIT%20No%20Attribution-blue.svg)](LICENSE)
[![Huawei Cloud](https://img.shields.io/badge/Huawei%20Cloud-IAM%20Auth-red.svg)](https://support.huaweicloud.com/intl/en-us/)

> One-click GPU instance diagnostics collector (single script: health / driver / topology / firmware + OBS archiving)
> GPU Diagnostics Collector

[中文版](README.cn.md)

## 1. Overview

This practice provides **one-click GPU instance fault diagnostics collection** for scenarios such as **Xid / NVLink / ECC / training-hang troubleshooting**. It targets platform / infrastructure engineers.

- A single script aggregates 50+ diagnostic items (nvidia-smi / bug-report / NVLink / ECC / Xid / firmware / Fabric Manager)
- Timeout / fallback / per-step pass-fail tracking (resilient against hung GPUs)
- Centralized archiving to OBS plus a summary report

## 2. Table of Contents

1. [Overview](#1-overview) 2. [Table of Contents](#2-table-of-contents) 3. [Architecture](#3-architecture) 4. [Highlights](#4-highlights) 5. [Cloud Services Used](#5-cloud-services-used) 6. [Prerequisites](#6-prerequisites) 7. [Quick Start](#7-quick-start) 8. [Step-by-Step Deployment](#8-step-by-step-deployment) 9. [Usage / Verification](#9-usage--verification) 10. [Cleanup](#10-cleanup) 11. [Details](#11-details) 12. [Dependencies](#12-dependencies) 13. [FAQ](#13-faq--troubleshooting) 14. [Contributing](#14-contributing) 15. [License](#15-license) 16. [Maintainers](#16-maintainers)

## 3. Architecture

```
┌────────────────────────────────────────────────────────────────────┐
│        GPU Cloud Server (nvidia-smi / nvlink / bug-report)         │
│                采集 50+ 诊断项：超时 / 回退 / 容错                 │
└──────────────────────────────────┬─────────────────────────────────┘
                                   │ 采集诊断项
                                   ▼
┌────────────────────────────────────────────────────────────────────┐
│         Timestamped directory / tar.gz (with summary.txt)          │
└──────────────────────────────────┬─────────────────────────────────┘
                                   │ obsutil cp / sync
                                   ▼
┌────────────────────────────────────────────────────────────────────┐
│             OBS Object Storage (centralized archiving)             │
│                       集中归档，支持团队分析                       │
└────────────────────────────────────────────────────────────────────┘
```

## 4. Highlights

- Single script, zero deployment dependencies; works with the standard NVIDIA toolchain
- Hang-resistant: timeouts, graceful fallback when a tool is missing, per-step pass/fail
- Centralized archiving: upload to OBS (directory or tar mode)

## 5. Cloud Services Used

| Service | Purpose | Notes |
|---|---|---|
| ECS (GPU flavor) | Diagnostic target | NVIDIA driver required |
| OBS | Archive for diagnostic records | Uploaded via obsutil |

## 6. Prerequisites

| Item | Requirement | Notes |
|---|---|---|
| Account | Huawei Cloud ECS with GPU flavor | `nvidia-smi` must work |
| obsutil | Installed and configured | Write permission on the OBS bucket |
| Bash | 4+ | — |
| Extended mode | ibv_devinfo / numactl / ipmitool | Optional |

## 7. Quick Start

```bash
git clone <repo-https-url> cloud-native-computing-gpu-diagnostics-collector
cd cloud-native-computing-gpu-diagnostics-collector
bash gpu-diag-collect.sh -b my-diagnostics-bucket
# OBS: obs://my-diagnostics-bucket/gpu-diag/<hostname>/<timestamp>/
```

## 8. Step-by-Step Deployment

1. Prepare a GPU cloud server with the NVIDIA driver and obsutil
2. `bash gpu-diag-collect.sh -b <bucket> [-p <prefix>] [--tar] [--extended]`
3. Inspect `summary.txt` and the uploaded OBS objects

## 9. Usage / Verification

> **Mandatory live check**: syntax and argument behaviour were verified before delivery (`bash -n`, `-h`). **Collecting real data requires a GPU cloud server** — the sandbox has no GPU, so that part is declared honestly rather than claimed.

```bash
bash -n gpu-diag-collect.sh            # syntax
bash gpu-diag-collect.sh -h            # arguments
bash gpu-diag-collect.sh -b my-bucket  # real run (GPU instance)
```

## 10. Cleanup

The script creates no persistent resources; delete the OBS objects as needed.

## 11. Details

- Collection: GPU status / driver and kernel logs / Fabric Manager / firmware inventory / extended mode
- Upload: obsutil cp (directory) or `--tar` (tar.gz)
- Summary: pass/fail per step

## 12. Dependencies

| Name | Purpose |
|---|---|
| nvidia-smi / nvidia-bug-report / nvlink | NVIDIA tools |
| journalctl / dmesg | Kernel logs |
| obsutil | OBS upload |

## 13. FAQ / Troubleshooting

- `nvidia-smi` not found? Check the driver; the script falls back when a tool is missing.
- `obsutil` not found? Install it and configure the access keys.
- A command hangs? The script enforces a timeout (300s for bug-report) and falls back.

## 14. Contributing

Issues / pull requests; run `bash -n` before submitting.

## 15. License

MIT No Attribution (see [LICENSE](LICENSE)).

## 16. Maintainers

[@<github-username>](https://github.com/<github-username>) via GitHub Issues / PRs.
