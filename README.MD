# vLLM + Qwen2.5-7B-Instruct (Ubuntu GPU reference)

Reference for running **Qwen/Qwen2.5-7B-Instruct** with vLLM on Ubuntu with an NVIDIA GPU.

## Prerequisites (Ubuntu)

- NVIDIA GPU with enough VRAM (~16GB+ recommended for 7B)
- NVIDIA drivers and CUDA (e.g. `nvidia-smi` works)
- Python 3.10+

## Setup

On Ubuntu/Debian, use a virtual environment so `pip` is allowed (avoids the “externally-managed-environment” error):

```bash
conda create -n vllm python=3.11
conda activate vllm
pip install --upgrade pip
pip install -r requirements.txt
```

If you don’t have `venv`, install it: `sudo apt install python3-venv python3-full`

## Run HTTP server

```bash
vllm serve Qwen/Qwen2.5-7B-Instruct --host 0.0.0.0 --port 8000 --gpu-memory-utilization 0.8
vllm serve BAAI/bge-m3 --host 0.0.0.0 --port 8001 --gpu-memory-utilization 0.2
```

API: `http://localhost:8000/v1/completions` or `/v1/chat/completions`.

## Model

- [Qwen/Qwen2.5-7B-Instruct](https://huggingface.co/Qwen/Qwen2.5-7B-Instruct) · [vLLM](https://docs.vllm.ai/)
