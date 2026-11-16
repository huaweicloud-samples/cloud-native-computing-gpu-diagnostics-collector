# 架构说明

`cloud-native-computing-gpu-diagnostics-collector` 提供 **GPU 实例一键诊断采集器**：单一 bash 脚本汇集 NVIDIA GPU 健康/驱动/拓扑/固件/内核日志，并上传 OBS 集中归档，供故障排查（Xid/NVLink/ECC/挂起）。

## 工作流

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

## 采集内容

- GPU 状态：nvidia-smi / nvidia-bug-report / NVLink 状态与错误 / ECC / row remapper / 拓扑
- 内核与驱动：Xid / NVLink 消息 / NVIDIA 驱动日志（journalctl/dmesg）
- Fabric Manager / 固件库存（VBIOS/驱动/InfoROM/GSP）
- 扩展模式：EFA 计数 / PCIe AER / NUMA / IPMI SEL / 实例元数据 / 内核 taint
