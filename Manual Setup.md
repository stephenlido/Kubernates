Done — I created a ready-to-use Vagrantfile (and placed helper scripts inside /vagrant) in a code document titled "Vagrantfile — Semi-automated K8s Cluster (master + 2 Workers)".

What I put in the Vagrantfile:

Installs containerd and configures systemd cgroup + pause:3.9 on all nodes.

Installs kubeadm/kubelet/kubectl on all nodes (but does not auto-run kubeadm init or kubeadm join).

Creates helper scripts under the shared /vagrant folder:

/vagrant/init_master.sh <MASTER_IP> — run on the master to initialize the control plane; it also writes /vagrant/join.sh.

/vagrant/join.sh — generated after init; run on each worker to join the cluster.

/vagrant/apply_calico.sh — apply Calico v3.27 (run on master after init).

Ensures host-only NIC promiscuous mode to allow pods/Calico traffic between VMs.

Next steps for you (brief):

vagrant destroy -f && vagrant up to recreate VMs with provisioning.

vagrant ssh master

Run /vagrant/init_master.sh 192.168.56.10 on the master — this will initialize kubeadm and create /vagrant/join.sh.

On each worker: vagrant ssh worker1 then sudo bash /vagrant/join.sh

On master: kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/calico.yaml or run /vagrant/apply_calico.sh.

Verify with kubectl get nodes and kubectl get pods -n kube-system