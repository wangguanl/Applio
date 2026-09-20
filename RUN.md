# 运行命令

- 项目：Applio（语音转换 / RVC Gradio UI）
- 生成时间：2026-09-08
- 运行方式：直接运行（`.venv` + `uv`，非 Docker）
- 硬件评估：**有压力 → 已按你的要求完成安装、未启动**（启动前请先停掉 Ollama 等占卡进程）

## 硬件评估

| 项 | 情况 |
|----|------|
| 官方门槛 | 推理分块按约 4–6GB 显存配置；16GB 总量满足 |
| 本机 | RTX 4080 16376 MiB；安装时曾有 Ollama 占卡，空闲约 6–7GB |
| 结论 | 你确认：完整安装、装完不跑；之后自行停服务再启动 |

## 环境准备（已完成）

```powershell
Set-Location E:\AI\local-voice\Applio
$env:Path = "E:\Programs\ffmpeg-master-latest-win64-gpl\bin;$env:Path"
$env:UV_INDEX_URL = "https://pypi.tuna.tsinghua.edu.cn/simple"
$env:HF_ENDPOINT = "https://hf-mirror.com"

uv venv --python 3.12 .venv
# torch / torchaudio：直链 cu128 Windows 轮子（避免解析成 CPU 版）
# 其余：清华 PyPI
# 预训练：hf-mirror.com（contentvec / rmvpe / fcpe / hifi-gan / refinegan）
# ffmpeg.exe / ffprobe.exe：硬链接到本机全局 ffmpeg，未再下载一份
```

验证（安装后已跑通）：

- `torch 2.11.0+cu128`，`cuda=True`，设备 RTX 4080
- `gradio` / `librosa` / `faiss` 等可 import
- 预训练权重已落盘于 `rvc/models/`

## 启动

- 推荐：`pwsh -NoProfile -File .\start.ps1`
- 说明：单服务 `webui`，无参直接启动（跳过菜单）。启动前建议停掉 Ollama 等占卡进程，确认 `nvidia-smi` 空闲显存充足。
- Docker：`pwsh -NoProfile -File .\start.ps1 -Mode docker`

等价手动：

```powershell
Set-Location E:\AI\local-voice\Applio
$env:Path = "E:\Programs\ffmpeg-master-latest-win64-gpl\bin;$env:Path"
$env:HF_ENDPOINT = "https://hf-mirror.com"
.\.venv\Scripts\python.exe app.py --open --port 6969
```

`start.ps1` 从 **6969** 起探测，占用则 +1 顺延。

## 验证

1. 浏览器打开 `http://127.0.0.1:<实际端口>`
2. Gradio 界面可加载；日志无 CUDA 致命错误
3.（可选）Inference 页加载模型并转换短音频

## 备注

- 镜像：PyPI → 清华；HF → `hf-mirror.com`；PyTorch → `download.pytorch.org/whl/cu128` 直链
- 未使用官方 Miniconda/`env\`，改用仓库内 `.venv`（Python 3.12）
- Docker daemon 当时不可用；单服务优先直接运行
- 本机改动（勿默认提交）：`RUN.md`、`start.ps1`、`.venv/`、模型权重、根目录 `ffmpeg.exe`/`ffprobe.exe` 硬链接
