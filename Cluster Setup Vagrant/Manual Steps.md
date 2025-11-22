# Kubernetes Cluster Using Vagrant (1 Master + 2 Workers)

This project provides a lightweight Kubernetes cluster using **Vagrant + kubeadm**, consisting of:

- **1 Master node**
- **2 Worker nodes**

All nodes are provisioned automatically with:

- containerd
- systemd cgroup configuration
- pause image (3.9)
- kubeadm / kubelet / kubectl
- Helper scripts inside `/vagrant` for cluster setup

---

## 📦 Features Included

### ✔️ Automated via Vagrantfile
- Installs **containerd** on all nodes  
- Configures **systemd cgroups**  
- Pulls **pause:3.9** image  
- Installs **kubeadm / kubelet / kubectl**  
- Does **not** auto-run `kubeadm init` or `join` (you use helper scripts)  
- Enables promiscuous mode for pod networking  
- `/vagrant` shared folder contains helper scripts  

---

## 📁 Included Helper Scripts (inside `/vagrant`)
```bash

1. init_master.sh <MASTER_IP>
Run this inside the **master node**:


/vagrant/init_master.sh 192.168.56.10


This script will:

Initialize the Kubernetes control plane

Configure containerd

Generate /vagrant/join.sh for worker nodes

2. join.sh

Generated automatically after running init_master.sh.

Run on each worker node:

sudo bash /vagrant/join.sh

3. apply_calico.sh

Installs Calico v3.27 CNI:

/vagrant/apply_calico.sh


Equivalent to:

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/calico.yaml

🚀 How to Use
1. Recreate VMs
vagrant destroy -f
vagrant up

2. SSH into the Master
vagrant ssh master


Initialize Kubernetes (using the host-only IP):

sudo bash /vagrant/init_master.sh 192.168.56.10


This generates /vagrant/join.sh.

3. Join Each Worker
Worker 1:
vagrant ssh worker1
sudo bash /vagrant/join.sh

Worker 2:
vagrant ssh worker2
sudo bash /vagrant/join.sh

4. Apply Pod Network (Calico)

Run on the master:

sudo bash /vagrant/apply_calico.sh

✅ Verify the Cluster

Check nodes:

kubectl get nodes


Check system pods:

kubectl get pods -n kube-system


You should see:

Master → Ready

Worker 1 → Ready

Worker 2 → Ready

(after Calico is applied)
