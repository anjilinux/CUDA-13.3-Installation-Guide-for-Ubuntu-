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
