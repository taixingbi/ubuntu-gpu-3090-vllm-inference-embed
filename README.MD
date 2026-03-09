# vLLM + Qwen2.5-7B (Ubuntu GPU)

Run **Qwen2.5-7B-Instruct** (inference) and **BAAI/bge-m3** (embeddings) with vLLM on Ubuntu using an NVIDIA GPU.

## Prerequisites

- **NVIDIA GPU** — 16GB+ VRAM for 7B inference
- **NVIDIA drivers + CUDA** — `nvidia-smi` works
- **Docker** — [Docker Engine](https://docs.docker.com/engine/install/)
- **NVIDIA Container Toolkit** — [Install guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html); [docker.md](docker.md) for Ubuntu fix. Verify: `docker run --rm --gpus all nvidia/cuda:12.0-base nvidia-smi`
- **Python 3.10+** — for local run only

## Run with Docker

```bash
docker compose up -d
```

- **Endpoints:** Inference `http://localhost:8000/v1/completions` and `/v1/chat/completions`; Embeddings `http://localhost:8001/v1/embeddings`
- **Logs:** `docker logs -f vllm-inference` or `docker logs -f vllm-embeddings`
- **Gated models:** set `HUGGING_FACE_HUB_TOKEN` in env or `.env`; models cached in a Docker volume.

**Test from another machine** (e.g. Mac): use host IP (e.g. `192.168.86.173`) or `localhost` on the same machine.

Chat completion:

```bash
curl http://SERVER_IP:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen/Qwen2.5-7B-Instruct","messages":[{"role":"user","content":"Hello"}],"max_tokens":32}'
```

Embeddings:

```bash
curl http://SERVER_IP:8001/v1/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model":"BAAI/bge-m3","input":"Hello"}'
```

## Local run (no Docker)

Use a venv on Ubuntu/Debian to avoid the externally-managed-environment error:

```bash
conda create -n vllm python=3.11
conda activate vllm
pip install --upgrade pip && pip install -r requirements.txt
```

Without conda: `sudo apt install python3-venv python3-full`, then use a `venv`.

**Inference:**

```bash
vllm serve Qwen/Qwen2.5-7B-Instruct --host 0.0.0.0 --port 8000 --gpu-memory-utilization 0.8
```

**Embeddings:**

```bash
vllm serve BAAI/bge-m3 --host 0.0.0.0 --port 8001 --gpu-memory-utilization 0.2
```

## Models

- [Qwen/Qwen2.5-7B-Instruct](https://huggingface.co/Qwen/Qwen2.5-7B-Instruct) — chat/completion
- [BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3) — embeddings
- [vLLM docs](https://docs.vllm.ai/)
