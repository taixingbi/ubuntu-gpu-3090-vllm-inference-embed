# NVIDIA Container Toolkit (Ubuntu)

Steps to fix a broken NVIDIA Container Toolkit repo and install it on Ubuntu/Debian.

## 1. Remove bad repo (if present)

```bash
sudo rm -f /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

## 2. Install dependencies

```bash
sudo apt-get update
sudo apt-get install -y --no-install-recommends ca-certificates curl gnupg2
```

## 3. Add NVIDIA repo key and list

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey \
  | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list \
  | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' \
  | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list > /dev/null
```

## 4. Install and configure

```bash
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

## 5. Verify GPU in Docker

```bash
docker run --rm --gpus all nvidia/cuda:12.3.0-base-ubuntu22.04 nvidia-smi
```

If that runs and shows your GPU, Docker can use the GPU for vLLM.