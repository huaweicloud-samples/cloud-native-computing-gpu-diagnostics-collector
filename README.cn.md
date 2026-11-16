# cloud-native-computing-gpu-diagnostics-collector / GPU 诊断采集器

[![License](https://img.shields.io/badge/License-MIT%20No%20Attribution-blue.svg)](LICENSE)
[![Huawei Cloud](https://img.shields.io/badge/Huawei%20Cloud-IAM%20Auth-red.svg)](https://support.huaweicloud.com/intl/en-us/)
[English](README.md)

> GPU 实例一键诊断采集器（单脚本：健康/驱动/拓扑/固件 + OBS 归档）
> GPU Diagnostics Collector

## 1. 简介 / Overview

**中文**：本实践提供 **GPU 实例故障诊断一键采集** 能力，用于 **Xid / NVLink / ECC / 训练挂起等故障排查** 场景。面向平台 / 基础设施工程师。

- 单脚本汇集 50+ 诊断项（nvidia-smi / bug-report / NVLink / ECC / Xid / 固件 / Fabric Manager）
- 超时 / 回退 / 每步 pass-fail 跟踪（抗挂死 GPU）
- 上传 OBS 集中归档 + summary 报告

## 2. 目录

1. [简介](#1-简介--overview-critical) 2. [目录](#2-目录) 3. [架构图](#3-架构图) 4. [方案亮点](#4-方案亮点) 5. [涉及云服务](#5-涉及云服务) 6. [前置条件](#6-前置条件-critical) 7. [快速开始](#7-快速开始-critical) 8. [分步部署](#8-分步部署) 9. [使用方法 / 验证](#9-使用方法--验证-critical) 10. [清理资源](#10-清理资源) 11. [详细说明](#11-详细说明) 12. [依赖](#12-依赖) 13. [FAQ](#13-faq--故障排除) 14. [贡献指南](#14-贡献指南) 15. [许可证](#15-许可证-critical) 16. [联系方式](#16-联系方式--维护者-critical)

## 3. 架构图

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

## 4. 方案亮点

- 单脚本、零部署依赖；NVIDIA 工具链通用（华为云 GPU ECS 同款）
- 抗挂死：超时 + 缺失工具回退 + 每步 pass/fail
- 集中归档：OBS 上传（目录/tar 两种）

## 5. 涉及云服务

| 服务 | 用途 | 备注 |
|---|---|---|
| 云服务器 GPU 规格 | 诊断对象 | NVIDIA 驱动 |
| OBS | 诊断档案归档 | obsutil 上传 |

## 6. 前置条件

| 项 | 要求 | 说明 |
|---|---|---|
| 账号 | 华为云 GPU 云服务器 | nvidia-smi 可用 |
| obsutil | 已配置 | 写 OBS 权限 |
| Bash | 4+ | — |
| 扩展模式 | ibv_devinfo/numactl/ipmitool | 可选 |

## 7. 快速开始

```bash
git clone <repo-https-url> cloud-native-computing-gpu-diagnostics-collector
cd cloud-native-computing-gpu-diagnostics-collector
bash gpu-diag-collect.sh -b my-diagnostics-bucket
# OBS: obs://my-diagnostics-bucket/gpu-diag/<hostname>/<timestamp>/
```

## 8. 分步部署

1. 准备 GPU 云服务器 + 驱动 + obsutil
2. `bash gpu-diag-collect.sh -b <bucket> [-p <prefix>] [--tar] [--extended]`
3. 检查 oss summary.txt / OBS 对象

## 9. 使用方法 / 验证

> **强制实测**：本脚本在交付前已验证语法与参数行为（`bash -n`、`-h`）；**真机采集需 GPU 云服务器**（沙箱无 GPU，如实声明）。

```bash
bash -n gpu-diag-collect.sh            # 语法
bash gpu-diag-collect.sh -h            # 参数
bash gpu-diag-collect.sh -b my-bucket  # 真机执行（GPU 实例）
```

## 10. 清理资源

脚本无持久资源；OBS 对象按需删除。

## 11. 详细说明

- 采集：GPU 状态 / 驱动与内核日志 / Fabric Manager / 固件库存 / 扩展模式
- 上传：obsutil cp（目录）或 `--tar`（tar.gz）
- summary：每步 pass/fail

## 12. 依赖

| 名称 | 用途 |
|---|---|
| nvidia-smi / nvidia-bug-report / nvlink | NVIDIA 工具 |
| journalctl / dmesg | 内核日志 |
| obsutil | OBS 上传 |

## 13. FAQ / 故障排除

- 无 nvidia-smi？确认驱动；脚本缺工具回退
- obsutil not found？确认安装与访问密钥配置
- 挂死命令？脚本超时（300s bug-report）与 fallback

## 14. 贡献指南

Issue / PR；提交前 `bash -n`。

## 15. 许可证

MIT No Attribution（见 LICENSE）。

## 16. 联系方式 / 维护者

[@<github-username>](https://github.com/<github-username>) via GitHub Issues / PR。
