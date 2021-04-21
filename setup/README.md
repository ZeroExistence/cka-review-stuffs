# Setup scripts
These are the scripts to deploy kubernetes with crio as container runtime.

## Prerequisite
1. Remove swap partition.
```
swapon -s
swapoff [SWAP PARTITION]
```
2. Setup hostname.
```
hostnamectl set-hostname ubuntu-m01.moe.ph
```
3. Clone this git repository, and go to setup folder.
```
git clone https://github.com/ZeroExistence/cka-review-stuffs.git
cd cka-review-stuffs/setup
```
4. Set versions needed into master and worker scripts.
```
export VERSION=1.20
export K8SVERSION=1.20.2-00
export OS=xUbuntu_20.04
```
5. Set your API endpoint in master-node-setup.sh. Use private IP to bind it to private network.
```
export MASTER_NODE_IP=192.168.146.5
```

## Master node installation.
1. Run master-node-setup.sh script.
```
bash master-node-setup.sh
```
   ### Do this for failed setup.
   2. To clean-up failed installation, use the command below.
   ```
   kubeadm reset
   ```
   3. Re-run master-node-setup.sh after clean-up.
   ```
   bash master-node-setup.sh
   ```

## Worker node installation.
1. Run worker-setup.sh
```
bash worker-setup.sh
```
2. Join the master node cluster using the kubeadm join command.
```
kubeadm join [MASTER_NODE]:6443 --token l7lhb4.dqu2yvltiv6sgfa0 \
>     --discovery-token-ca-cert-hash sha256:143e0e6d3e0266a1951eaffac0c86c14e527d985ed1cfa4d2e2420701b7d8546
```
