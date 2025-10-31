# ☸️ Kubernetes Single-Master Cluster using Kubeadm (Ubuntu)

This repository provides a step-by-step guide to set up a **single-master Kubernetes cluster** using **Kubeadm** on **Ubuntu 22.04+**.  
It covers all necessary configurations — from installing dependencies to deploying the cluster network (CNI) and joining worker nodes.

---

## 🧩 Prerequisites

### Hardware Requirements
| Role | vCPU | RAM | Storage |
|------|------|-----|----------|
| Master Node | 2 | 2 GB+ | 20 GB |
| Worker Node | 1 | 1 GB+ | 20 GB |

### System Requirements
- Ubuntu 20.04 / 22.04 (64-bit)
- Root or sudo privileges
- Internet connectivity
- Unique hostname, MAC address, and product UUID
- Swap must be disabled

---

## ⚙️ Step 1: System Preparation

```bash
# Update and upgrade system
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y apt-transport-https ca-certificates curl gpg

# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

# Enable required kernel modules
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Apply sysctl parameters for Kubernetes networking
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system


🐳 Step 2: Install Container Runtime (Containerd)

# Install containerd
sudo apt install -y containerd

# Configure containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null

# Use systemd as cgroup driver
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# Restart and enable containerd
sudo systemctl restart containerd
sudo systemctl enable containerd


☸️ Step 3: Install Kubernetes Components

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
  sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

🧠 Step 4: Initialize the Master Node

# Initialize cluster (default pod CIDR for Calico)
sudo kubeadm init --pod-network-cidr=192.168.0.0/16


After the setup completes, configure kubectl for your user:

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

🌐 Step 5: Install Network Add-on (CNI)

kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml


