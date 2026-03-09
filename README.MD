# vLLM + Qwen2.5-7B-Instruct (Ubuntu GPU)

Reference for running **Qwen/Qwen2.5-7B-Instruct** (inference) and **BAAI/bge-m3** (embeddings) with vLLM on Ubuntu with an NVIDIA GPU.

## Prerequisites

- **NVIDIA GPU** — ~16GB+ VRAM recommended for 7B inference
- **NVIDIA drivers + CUDA** — `nvidia-smi` should work
- **Python 3.10+**

## Setup

Use a virtual environment to avoid the “externally-managed-environment” error on Ubuntu/Debian:

```bash
conda create -n vllm python=3.11
conda activate vllm
pip install --upgrade pip
pip install -r requirements.txt
```

> Without conda: `sudo apt install python3-venv python3-full`, then create and use a `venv` instead.

## Run services

**Inference (Qwen2.5-7B):**

```bash
vllm serve Qwen/Qwen2.5-7B-Instruct --host 0.0.0.0 --port 8000 --gpu-memory-utilization 0.8
```

**Embeddings (BGE-M3):**

```bash
vllm serve BAAI/bge-m3 --host 0.0.0.0 --port 8001 --gpu-memory-utilization 0.2
```

## API

- **Inference:** `http://localhost:8000/v1/completions` or `/v1/chat/completions`
- **Embeddings:** `http://localhost:8001/v1/embeddings`

## Models

- [Qwen/Qwen2.5-7B-Instruct](https://huggingface.co/Qwen/Qwen2.5-7B-Instruct) — chat/completion
- [BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3) — embeddings  
- [vLLM docs](https://docs.vllm.ai/)
