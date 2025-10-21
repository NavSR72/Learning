# Kubernetes v1.26 Setup on RHEL/CentOS 8 with CRI-O and Calico

## 1. Load Required Kernel Modules

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

## 2. Set Required Sysctl Parameters

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

## 3. Set Up CRI-O Repositories

```bash
export VERSION=1.26
export OS=CentOS_8

sudo curl -L -o /etc/yum.repos.d/devel:kubic:libcontainers:stable.repo \
  https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/devel:kubic:libcontainers:stable.repo

sudo curl -L -o /etc/yum.repos.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.repo \
  https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:cri-o:/$VERSION/$OS/devel:kubic:libcontainers:/stable:cri-o:$VERSION.repo
```

## 4. Install and Configure CRI-O

```bash
sudo dnf install -y cri-o

# Ensure CRI-O uses systemd as cgroup manager
sudo sed -i 's/^#*cgroup_manager.*/cgroup_manager = "systemd"/' /etc/crio/crio.conf

sudo systemctl enable crio
sudo systemctl start crio
```

## 5. Add Kubernetes Repository

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.26/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.26/rpm/repodata/repomd.xml.key
EOF
```

## 6. Install Kubernetes Components

```bash
sudo dnf install -y kubelet-1.26.1 kubeadm-1.26.1 kubectl-1.26.1
sudo systemctl enable --now kubelet
```

## 7. Disable Swap

```bash
sudo swapoff -a
sudo sed -i '/swap/s/^/#/' /etc/fstab
free -h
```

## 8. Configure Kubelet for CRI-O

```bash
cat <<EOF | sudo tee /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS="--cgroup-driver=systemd --container-runtime=remote --container-runtime-endpoint=unix:///var/run/crio/crio.sock"
EOF

sudo systemctl daemon-reexec
sudo systemctl restart kubelet
```

## 9. Disable SELinux

```bash
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config
```

## 10. Enable IPv6 (Required by CRI-O)

```bash
sudo tee -a /etc/sysctl.conf <<EOF
net.ipv6.conf.all.disable_ipv6=0
net.ipv6.conf.default.disable_ipv6=0
EOF

sudo sysctl --system
```

## 11. Initialize Kubernetes Cluster

```bash
sudo kubeadm init \
  --cri-socket unix:///var/run/crio/crio.sock \
  --pod-network-cidr=192.168.0.0/16
```

## 12. Set Up Kubeconfig for Current User

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Verify node status:

```bash
kubectl get nodes
```

## 13. Install Calico CNI

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```

Monitor progress:

```bash
watch kubectl get pods -n kube-system
```

Wait until all pods (including `coredns`, `calico-node`, and `calico-kube-controllers`) show Running.

## 14. Verify Cluster

```bash
kubectl get nodes -o wide
kubectl get pods -A
```

Expected output:

```
STATUS   ROLES           AGE   VERSION
Ready    control-plane   xxm   v1.26.1
```
