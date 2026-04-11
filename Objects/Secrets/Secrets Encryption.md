**Secrets Encryption at Rest** ensures that Kubernetes [[Secrets]] objects are stored in encrypted form in [[etcd]], rather than in plaintext base64

* By default, Secrets are stored as base64-encoded plaintext in etcd because anyone with etcd access can read them
* Enabling encryption at rest protects Secret data even if an etcd backup or etcd storage is compromised
* Configured via an [[Encryption Configuration]] object passed to the [[kube-apiserver]]

## Enabling Encryption

### Step 1: Generate an Encryption Key

```Bash
head -c 32 /dev/urandom | base64
# Outputs a 32-byte base64 key
```

### Step 2: Create the EncryptionConfiguration

```YAML
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: BASE64_ENCODED_32_BYTE_KEY
      - identity: {}   # Fallback: allows reading unencrypted secrets during migration
```

### Step 3: Configure the kube-apiserver

Add to the kube-apiserver manifest (`/etc/kubernetes/manifests/kube-apiserver.yaml`):

```YAML
- --encryption-provider-config=/etc/kubernetes/encryption-config.yaml
```

Also mount the file into the pod if running as a Static Pod.

### Step 4: Re-encrypt Existing Secrets

Existing secrets are not automatically re-encrypted (You must force a re-write to encrypt them):

```Bash
kubectl get secrets -A -o json | kubectl replace -f -
```

## Verifying Encryption

```Bash
# Read the raw etcd value — should be opaque bytes, not recognisable YAML
etcdctl get /registry/secrets/default/MY_SECRET \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key | hexdump -C | head
```

An encrypted value starts with `k8s:enc:aescbc:v1:` prefix.

## Key Rotation

To rotate encryption keys without downtime:
1. Add the new key as `key2` at the top of the `keys` list (new writes use `key2`)
2. Keep `key1` in the list (existing secrets can still be read)
3. Re-encrypt all secrets: `kubectl get secrets -A -o json | kubectl replace -f -`
4. Remove `key1` from the configuration once all secrets are re-encrypted
