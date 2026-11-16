# 使用指导书

## 1. 前置条件

- 华为云 GPU 云服务器（Pi2/G5 等）+ NVIDIA 驱动 + nvidia-smi
- Bash 4+ / root（推荐）
- obsutil 已配置（访问密钥具备目标 OBS 桶写权限）
- 扩展模式：ibv_devinfo/ibstat、numactl、dmidecode、ipmitool

## 2. 使用

```bash
# 基础采集并上传 OBS
bash gpu-diag-collect.sh -b my-diagnostics-bucket

# 指定前缀 + 压缩
bash gpu-diag-collect.sh -b my-bucket -p prod/gpu01 --tar

# 扩展模式
bash gpu-diag-collect.sh -b my-bucket --extended
```

## 3. 产物

- `${OUTDIR}/summary.txt`（每步 pass/fail）
- 采集目录或 tar.gz → `obs://<bucket>/<prefix>`

## 4. 清理

本脚本不在 GPU 实例留持久资源（仅输出目录）；OBS 对象按需删除
