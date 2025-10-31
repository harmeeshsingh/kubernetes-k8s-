# kubernetes-k8s-
kubernetes(k8s)

## Bare matel installation of kubernetes 
# Step1 Prerequisites to install kubernetes 
    https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
# Step 2 Necesary ports to be open 
https://kubernetes.io/docs/reference/networking/ports-and-protocols/

# Step 3 Install docker in all the nodes 
https://docs.docker.com/engine/install/ubuntu/

# Step 4 reboot the system
sudo init 6 #it will only restart the kernal
or 
sudo reboot #it will reboot hardware and kernal

# Step 5 Setup kubeadm, kubectl, kubelet
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

# Step 6 By default containerd is the primary container runtime for kubernetes which we need to change to docker. Use following command to configure this. 
sudo rm /etc/containerd/config.toml   
sudo systemctl restart containerd 
sudo echo "1" >  /proc/sys/net/ipv4/ip_forward
sudo modprobe bridge      
sudo modprobe br_netfilter

# step 7 Initalization of master node
sudo kubeadm init --pod-network-cidr=192.168.0.0/16

# step 8 Add calico for the master node 
https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart




