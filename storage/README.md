# Services Related Matters
This folder discusses matters related to services within kubernetes.

## Demo
1. K8S Volumes.
   * hostPath
     1. Inspect the vol-hostpath-demo.yaml file.
     2. Deploy the vol-hostpath-demo.yaml.
     ```
     kubectl apply -f vol-hostpath-demo.yaml
     ```
     3. Check the node where the vol-hostpath-demo pod are running.
     ```
     kubectl get pods -o wide
     ```
     4. Login into the node.
     5. Check the output of the /tmp/hostpath-vol/data file.
   * emptyDir
     1. Inspect the vol-emptydir-demo.yaml file.
     2. Deploy the vol-emptydir-demo.yaml.
     ```
     kubectl apply -f vol-emptydir-demo.yaml
     ```
     3. Go into pod, and tail follow the data file using the alpine-reader container.
     ```
     kubectl exec vol-emptydir-demo-[POD_NAME] -c alpine-reader -- tail -f /input/data
     ```
2. K8S Persistent Volumes.
   * Setup
     1. Install nfs-kernel-server in master node.
     ```
     apt-get install nfs-kernel-server -y
     ```
     2. Run this script to setup nfs share directories. Add all the private IP addresses of all K8S nodes in the script.
     ```
     mkdir -p /nfs/share{1..9}
     export K8S_NODES="192.168.218.254 192.168.180.130"
     for K8S_IP in $K8S_NODES;
     do for K8S_SHARE in `seq 1 9`;
     do echo "/nfs/share${K8S_SHARE} ${K8S_IP}(rw,no_root_squash)" >> /etc/exports;
     done;
     done;
     exportfs -a
     ```
     3. Install nfs-common into all k8s worker nodes.
     ```
     apt-get install nfs-common -y
     ```
   * Persistent volume
     1. Inspect vol-nfs-persistent-pv-demo.yaml file.
     2. Change the IP address in the NFS server setup.
     ```
     sed -i -e 's/192.168.180.130/[NFS_MASTER_NODE_IP]/g' vol-nfs-persistent-pv-demo.yaml
     ```
     3. Deploy the yaml file.
     ```
     kubectl apply -f vol-nfs-persistent-pv-demo.yaml
     ```
     4. Inspect the pv and pvc resources.
     ```
     kubectl get pv,pvc -o wide
     ```
   * Deployment demo
     1. Inspect vol-deploy-persistent-demo.yaml.
     2. Deploy the yaml file.
     ```
     kubectl apply -f vol-deploy-persistent-demo.yaml
     ```
     3. Inspect pods, and check the mount share in nfs server.
   * Statefulset demo
     1. Inspect vol-deploy-stateful-demo.yaml.
     2. Deploy the yaml file.
     ```
     kubectl apply -f vol-deploy-stateful-demo.yaml
     ```
     3. Get the pvc resources.
     ```
     kubectl get pvc -o wide
     ```
