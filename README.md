CUDA 13.3 Installation on Ubuntu 24.04 LTS (Step-by-Step)
Step 1 - Update Ubuntu
sudo apt update
sudo apt upgrade -y
sudo reboot

Verify:

lsb_release -a
uname -r
Step 2 - Verify Ubuntu Version
cat /etc/os-release

lsb_release -a

hostnamectl
Step 3 - Verify Hardware

CPU

lscpu

RAM

free -h

Disk

df -h

PCI Devices

lspci

NVIDIA GPU

lspci | grep -i nvidia

Detailed Display Info

sudo lshw -C display
Step 4 - Check Recommended NVIDIA Driver
ubuntu-drivers devices

Expected output:

driver : nvidia-driver-595-open recommended
Step 5 - Install NVIDIA Driver
sudo apt install nvidia-driver-595-open -y

Reboot

sudo reboot
Step 6 - Verify Driver
nvidia-smi

Check Driver Version

cat /proc/driver/nvidia/version

Kernel Module

modinfo nvidia | grep version

Driver Loaded

lsmod | grep nvidia
Step 7 - Install Docker

Remove old Docker

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
    sudo apt remove -y $pkg
done

Install prerequisites

sudo apt install -y ca-certificates curl gnupg

Create keyring

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

Add repository

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list

Install Docker

sudo apt update

sudo apt install -y \
docker-ce \
docker-ce-cli \
containerd.io \
docker-buildx-plugin \
docker-compose-plugin

Start Docker

sudo systemctl enable docker

sudo systemctl start docker

Verify

docker version

docker info
Step 8 - Install NVIDIA Container Toolkit

Import repository

curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | \
sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

Add repository

curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

Install

sudo apt update

sudo apt install -y nvidia-container-toolkit

Configure Docker

sudo nvidia-ctk runtime configure --runtime=docker

Restart Docker

sudo systemctl restart docker

Verify

nvidia-ctk --version

docker info | grep -A5 Runtimes
Step 9 - Verify GPU Inside Docker
docker run --rm --gpus all \
nvidia/cuda:13.3.0-base-ubuntu24.04 \
nvidia-smi
Step 10 - Install CUDA Repository

Download keyring

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb

Install keyring

sudo dpkg -i cuda-keyring_1.1-1_all.deb

Update

sudo apt update
Step 11 - Install CUDA Toolkit
sudo apt install -y cuda-toolkit

Verify installed packages

dpkg -l | grep cuda
Step 12 - Configure Environment
echo 'export CUDA_HOME=/usr/local/cuda' >> ~/.bashrc

echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc

echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

source ~/.bashrc

hash -r

Verify

which nvcc

nvcc --version

/usr/local/cuda/bin/nvcc --version
Step 13 - Remove Old Ubuntu CUDA Toolkit (Optional)

Check if installed

apt-cache policy nvidia-cuda-toolkit

Simulate removal

sudo apt remove --simulate \
nvidia-cuda-toolkit \
nvidia-cuda-dev \
nvidia-cuda-gdb \
nvidia-cuda-toolkit-doc

Remove

sudo apt purge -y \
nvidia-cuda-toolkit \
nvidia-cuda-dev \
nvidia-cuda-gdb \
nvidia-cuda-toolkit-doc

sudo apt autoremove -y

Verify

dpkg -l | grep cuda

which nvcc

nvcc --version
Step 14 - Test CUDA Compilation

Create program

nano hello.cu

Compile

nvcc hello.cu -o hello

Run

./hello
Step 15 - Final Verification
nvidia-smi

nvcc --version

docker run --rm --gpus all nvidia/cuda:13.3.0-base-ubuntu24.04 nvidia-smi

docker info | grep Runtime

nvidia-ctk --version

==================
Since your objective is to become a Generative AI / RAG / MLOps Engineer, I'll teach you in the same order used by NVIDIA and production AI teams.

Complete Roadmap
Module 0 ✅ (Completed)
────────────────────────────────────
Ubuntu
NVIDIA Driver
CUDA Toolkit
Docker
NVIDIA Container Toolkit

            ↓

Module 1
CUDA Programming

            ↓

Module 2
GPU Architecture

            ↓

Module 3
PyTorch GPU

            ↓

Module 4
TensorRT + cuDNN

            ↓

Module 5
LLM Inference (vLLM)

            ↓

Module 6
RAG Pipeline

            ↓

Module 7
Kubernetes GPU Operator

            ↓

Module 8
Production AI Platform
Module 1 — CUDA Programming

We'll cover this as a complete course.

Chapter 1 — GPU Computing Fundamentals
What is CUDA?
Why was CUDA invented?
CPU vs GPU
SIMD vs SIMT
Streaming Multiprocessors (SMs)
CUDA C++
CUDA Runtime
CUDA Driver API
Chapter 2 — GPU Hardware

Your RTX 3050 contains:

CUDA Cores
SMs
L2 Cache
Global Memory
Shared Memory
Registers
Warp Scheduler
Tensor Cores
RT Cores

We'll learn what each component does.

Chapter 3 — CUDA Execution Model

You'll understand:

Application
      │
Kernel Launch
      │
Grid
      │
Blocks
      │
Threads

For example:

kernel<<<2,256>>>();

means

2 Blocks

Each Block

256 Threads

Total = 512 GPU Threads
Chapter 4 — Writing CUDA Programs

You'll write programs such as:

Hello CUDA

Vector Addition

Matrix Addition

Matrix Multiplication

Reduction

Prefix Sum

Image Blur

Histogram

Convolution

Softmax

Neural Network Layer
Chapter 5 — CUDA Memory

You'll learn:

Registers

Shared Memory

Constant Memory

Texture Memory

Global Memory

Unified Memory

This is one of the most important CUDA topics.

Chapter 6 — CUDA Performance

Topics include:

Occupancy
Warp divergence
Memory coalescing
Synchronization
Streams
Events
Asynchronous copies
Chapter 7 — CUDA Libraries

We'll use:

cuBLAS
cuDNN
cuFFT
NCCL
TensorRT
Chapter 8 — AI Frameworks

We'll connect CUDA with:

PyTorch

TensorFlow

ONNX Runtime

vLLM

TensorRT-LLM
Chapter 9 — Kubernetes

Then we'll deploy GPU workloads:

Docker

↓

NVIDIA Device Plugin

↓

GPU Operator

↓

DCGM Exporter

↓

Prometheus

↓

Grafana

↓

LLM Pods
Practical Labs

We'll complete around 30 hands-on labs, including:

Lab 1: Hello CUDA
Lab 2: Thread IDs
Lab 3: Block IDs
Lab 4: Vector Addition
Lab 5: Matrix Addition
Lab 6: Matrix Multiplication
Lab 7: Shared Memory
Lab 8: Streams
Lab 9: Unified Memory
Lab 10: Performance Optimization
...
Lab 30: GPU-accelerated RAG deployment
Outcome

By the end, you'll be able to explain and implement the complete path from GPU hardware to production AI:

RTX 3050
     │
CUDA
     │
cuDNN
     │
PyTorch
     │
Transformers
     │
vLLM
     │
FastAPI
     │
Docker
     │
Kubernetes
     │
Production RAG
