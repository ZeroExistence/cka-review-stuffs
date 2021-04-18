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
3. Set versions needed into master and worker scripts.
```
export VERSION=1.20
export K8SVERSION=1.20.2-00
export OS=xUbuntu_20.04
```
4. Do full system update.
```
apt-get update && apt-get upgrade -y
```

## Master node installation. (Networkless setup)
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
kubeadm join ~~~
```
