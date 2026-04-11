
Check node status
```Bash
kubectl get nodes
```

View service logs
```Bash
sudo journalctl -u kube-apiserver
```

View Pod logs
```Bash
kubectl get pods -n NAMESPACE
```

## Diagnosing Control Plane Failures

### Step 1: Check node and pod status

```Bash
kubectl get nodes
kubectl get pods -n kube-system
```

### Step 2: Check component logs

```Bash
# Static pod logs (kubeadm clusters)
kubectl logs -n kube-system kube-apiserver-NODE
kubectl logs -n kube-system kube-controller-manager-NODE
kubectl logs -n kube-system kube-scheduler-NODE
kubectl logs -n kube-system etcd-NODE

# systemd service logs
journalctl -u kube-apiserver -f
journalctl -u etcd -f
```

### Step 3: Check static pod manifests

```Bash
ls /etc/kubernetes/manifests/
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

### Step 4: Check certificates

```Bash
kubeadm certs check-expiration
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -dates
```

## Common Causes

| Symptom | Likely Cause |
|---|---|
| [[kube-apiserver]] unreachable | [[etcd]] down, cert expired, wrong `--etcd-servers` flag |
| Pods not scheduling | [[kube-scheduler]] not running or not leader |
| Deployments not reconciling | [[kube-controller-manager]] not running |
| etcd [[Architecture/Leader Election]] failures | Clock skew > 1s between etcd nodes |

## etcd Health Check
```Bash
etcdctl endpoint health \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

etcdctl member list --write-out=table \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

## API Server Unreachable

```Bash
# On the control plane node directly
systemctl status kube-apiserver    # If running as a service

# For kubeadm (Static Pod):
crictl ps | grep kube-apiserver    # Check if container is running
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep -E "etcd-servers|cert"
journalctl -u kubelet -n 50 | grep -i apiserver

# Check certificate expiry
kubeadm certs check-expiration
```

## Recovering a Broken Static Pod

If a control plane Static Pod manifest was accidentally broken:

```Bash
# Restore from backup
cp /path/to/backup/kube-apiserver.yaml /etc/kubernetes/manifests/
# kubelet will restart the pod automatically within seconds
```
