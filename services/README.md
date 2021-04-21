# Services Related Matters
This folder discusses matters related to services within kubernetes.

## Demo
1. Services.
   * ClusterIP Service
     1. Create echo-cluster-service deployment.
     ```
     kubectl create deployment echo-cluster-service --image=k8s.gcr.io/echoserver:1.10 --port=8080 --replicas=2
     ```
     2. Expose echo-cluster-service using cluster ip.
     ```
     kubectl expose deployment/echo-cluster-service --name=echo-cluster-service
     ```
     3. Check services resources.
     ```
     kubectl get svc
     ```
     4. Create curl-service-demo pod.
     ```
     kubectl run curl-service-demo --image=curlimages/curl --command -- /bin/sh -c "sleep infinity"
     ```
     5. Curl the echo-cluster-service multiple times.
     ```
     kubectl exec curl-service-demo -- curl echo-cluster-service.default.svc.cluster.local:8080
     ```
   * NodePort Service
     1. Expose echo-cluster-service with nodePort type.
     ```
     kubectl expose deployment/echo-cluster-service --name=echo-nodeport-service --type=NodePort --port=8443 --target-port=8080
     ```
     2. Get services resources.
     ```
     kubectl get svc -o wide
     ```
     3. Curl the IP address of your node, with the given nodeport.
     ```
     curl [NODE_IP_ADDRESS]:[NODEPORT]
     ```
2. Service DNS Service
   1. Curl the services that you created using the service DNS names.
   ```
   kubectl exec curl-service-demo -- curl echo-cluster-service.default.svc.cluster.local:8080
   kubectl exec curl-service-demo -- curl echo-nodeport-service.default.svc.cluster.local:8443
   ```
3. Ingress
   1. Install ingress-nginx ingress controller.
   ```
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.45.0/deploy/static/provider/baremetal/deploy.yaml
   ```
   2. Add ingress rule for echo-cluster-service services by applying ingress-demo.yaml.
   ```
   kubectl apply -f ingress-demo.yaml
   ```
   3. Get the nodeports used by ingress-nginx.
   ```
   kubectl get svc -n ingress-nginx ingress-nginx-controller -o wide
   ```
   4. Curl the ingress port with the specified path in the ingress rule.
   ```
   curl http://NODE_IP_ADDRESS:INGRESS_HTTTP_PORT:/echo
   ```
   5. To try named port in ingress, edit echo-cluster-service resource, and add name to port.
   ```
   kubectl edit svc echo-cluster-service
   ```
   6. Edit the ingress-demo ingress, and use namedport instead of number.
   ```
   kubectl edit ingress ingress-demo
   ```
   7. Curl the ingress controller again.
   ```
   curl http://NODE_IP_ADDRESS:INGRESS_HTTTP_PORT:/echo
   ```

---

## Additional YAML file for references
#### ingress-wildcard-demo.yaml
This ingress object defines a hostname with full path.
```
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host             Path  Backends
  ----             ----  --------
  demo.moe.ph
                   /   echo-cluster-service:8080 (10.10.112.70:8080,10.10.112.71:8080,10.10.252.198:8080)
Annotations:       nginx.ingress.kubernetes.io/rewrite-target: /
```
