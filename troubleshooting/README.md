# Troubleshooting Related Matters
This folder discusses matters related to troubleshooting within kubernetes.

## Demo
* Setup
  1. Deploy the faulty.yaml file.
  ```
  kubectl apply -f faulty.yaml
  ```

* Troubleshooting Nodes Scenarios.
  1. Node is in Not Ready state.
     1. Check node basic functionality(network and storage).
     2. Check kubelet and container runtime services.
     ```
     systemctl status kubelet;
     systemctl status crio;
     crio-status info;
     ```
     3. Check related logs of the involved services.
     ```
     journalctl -u kubelet;
     journalctl -u crio;
     less /var/log/syslog;
     ```
  2. Node is in Ready state, but not accepting pod schedule.
     1. Check if the node is cordoned.
     ```
     kubectl get nodes -o wide
     ```
     2. Check the current node taints.
     ```
     kubectl describe node [NODENAME];
     ```
     3. Remove taints to allow pod scheduling.
     ```
     kubectl taint node -l node-role.kubernetes.io/master= node- role.kubernetes.io/master:NoSchedule-
     ```

* Troubleshooting Deployment and Pod Scenarios.
  1. Troubleshoot nginx0[1234] deployments.
     1. Check current status.
     ```
     kubectl get deploy nginx01 -o wide
     ```
     2. Inspect pods.
     ```
     kubectl describe pod [NGINX_POD]
     ```
     3. Fix issue in the deployment object.
     ```
     kubectl edit deploy nginx01
     ```
  2. Troubleshoot python-app deployment.
     1. Check current status.
     ```
     kubectl get deploy python-app -o wide
     ```
     2. Inspect pods.
     ```
     kubectl describe pod [PYTHON_APP_POD]
     ```
     3. The issue seems to be the application. Edit deployment for  debugging. Change replicas to 1, and the command to sleep.
     ```
     kubectl edit deploy python-app --record
     ```
     4. Exec into the pod for application troubleshooting.
     5. Fix code.
     6. Rollback to previous deployment with correct command values.
     ```
     kubectl rollout history python-app
     kubectl rollout undo python-app
     ```
     7. Verify if the application is running now.
     ```
     kubectl rollout status python-app
     ```

* Troubleshooting Services.
  1. Check the connection to echo-cluster-service service.
  ```
  curl ECHO_CLUSTER_IP:8080
  ```
  2. Inspect echo-cluster-service service.
  ```
  kubectl describe svc echo-cluster-service
  kubectl get endpoints
  ```
  3. Fix echo-cluster-service.
  4. Re-check connection to service.
