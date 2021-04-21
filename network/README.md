# Network Related Matters
This folder discusses matters related to networking.

## Demo
1. CNI Plugin Installation.
   1. Setup a kubernetes first master cluster node. You can run the script in the setup folder of this repository.
   2. Add a worker node. Run script in the setup folder as well.
   3. Check nodes and pods status of the cluster.
   ```
   kubectl get nodes,pods -A -o wide
   ```
   4. Install calico CNI plugin in cluster.
   ```
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```
   5. Monitor status of nodes and pods again.
   ```
   kubectl get nodes,pods -A -o wide
   ```

2. Networking.
   1. Deploy echoserver pod.
   ```
   kubectl run echo1 --image=k8s.gcr.io/echoserver:1.10
   ```
   2. Deploy curl client pod.
   ```
   kubectl run curl1 --image=curlimages/curl --command -- /bin/sh -c "sleep infinity"
   ```
   3. Get pod ip addresses.
   ```
   kubectl get pods -o wide
   ```
   4. Access curl1 pod.
   ```
   kubectl exec curl1 -- curl [ECHO1 IP ADDRESS]
   ```

3. K8S DNS Demo.
   1. In curl access to demo, use pod DNS instead of IP address. Use this naming convention <i>ip-address.namespace</i>.pod.cluster.local.
   ```
   kubectl exec curl1 -- curl [ECHO1 DNS ADDRESS]
   ```

4. K8S Network Policy Demo
   1. Create resources using the net-pol-demo.yaml
   ```
   kubectl apply -f net-pol-demo.yaml
   ```
   2. Network policy demo for pod.
      1. Try running curl within the secure-network.
      ```
      export ECHO_SECURE_IP=[ECHO_SECURE_IP_ADDRESS]
      kubectl exec -ti -n secure-network curl-secure -- curl $ECHO_SECURE_IP
      kubectl exec -ti -n client curl-client-blocked -- curl $ECHO_SECURE_IP
      ```
      2. Apply network policy for echo-secure ingress policy, allowing curl-secure access only.
      ```
      kubectl apply -f net-pol-for-pods.yaml
      ```
      3. Try again the curl connection from both pods.
      ```
      kubectl exec -ti -n secure-network curl-secure -- curl $ECHO_SECURE_IP
      kubectl exec -ti -n client curl-client-blocked -- curl $ECHO_SECURE_IP
      ```
      4. Delete the net-pol-for-pods.yaml policy.
      ```
      kubectl delete -f net-pol-for-pods.yaml
      ```
   3. Network policy for namespace.
      1. Try running curl using the pods in the client namespace to echo pod of secure-network namespace.
      ```
      export ECHO_SECURE_IP=[ECHO_SECURE_IP_ADDRESS]
      kubectl exec -ti -n client curl-client-secure -- curl $ECHO_SECURE_IP
      kubectl exec -ti -n secure-network curl-blocked -- curl $ECHO_SECURE_IP
      ```
      2. Apply network policy for namespace.
      ```
      kubectl apply -f net-pol-for-namespace.yaml
      ```
      3. Try again the curl connection.
      ```
      kubectl exec -ti -n client curl-client-secure -- curl $ECHO_SECURE_IP
      kubectl exec -ti -n secure-network curl-blocked -- curl $ECHO_SECURE_IP
      ```
      4. Delete the net-pol-for-namespace.yaml
      ```
      kubectl delete -f net-pol-for-namespace.yaml
      ```

---

## Additional YAML file for references
#### net-pol-for-namespace-pod.yaml
This policy uses both namespaceSelector and podSelector. Both the selector should be true to allow access.
```
Spec:
  PodSelector:     app=echo-secure
  Allowing ingress traffic:
    To Port: 8080/TCP
    From:
      NamespaceSelector: project=client
      PodSelector: app=curl-client-secure
  Not affecting egress traffic
  Policy Types: Ingress
```

#### net-pol-for-multiple-pods.yaml
This policy uses multiple pod selectors for allowing multiple apps in single policy. Pod should either be app=curl-secure or app=curl-blocked.
```
Spec:
  PodSelector:     app=echo-secure
  Allowing ingress traffic:
    To Port: 8080/TCP
    From:
      PodSelector: app=curl-secure
    From:
      PodSelector: app=curl-blocked
  Not affecting egress traffic
  Policy Types: Ingress
```
