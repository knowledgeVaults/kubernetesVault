**[[Encryption Configuration]]** is a Kubernetes [[kube-apiserver]] configuration object that enables encryption of specific resource types at rest in [[etcd]]

* By default, Kubernetes stores all objects in etcd in plaintext including Secrets
* Enabling encryption at rest ensures that sensitive data is encrypted before being written to etcd storage
* Configuration is applied via the `--encryption-provider-config` flag on the kube-apiserver

## EncryptionConfiguration Object

```YAML
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
      - configmaps
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: BASE64_ENCODED_32_BYTE_KEY
      - identity: {}
```

* `resources`: the resource types to encrypt (e.g., `secrets`, `configmaps`)
* `providers`: ordered list of encryption providers; the first is used for **writing**, all are tried for **reading**
* `identity: {}`: no-op provider — reads/writes plaintext; placing it last ensures unencrypted existing data can still be read

## Encryption Providers

| Provider | Algorithm | Notes |
|---|---|---|
| `aescbc` | AES-CBC with PKCS#7 padding | Widely supported; requires 16/24/32-byte key |
| `aesgcm` | AES-GCM | Faster; not recommended for large key counts due to nonce reuse risk |
| `secretbox` | XSalsa20 + Poly1305 | Strong; requires 32-byte key |
| `kms` | External KMS (e.g., AWS KMS, GCP KMS) | Envelope encryption; key rotation managed externally |
| `identity` | None | No encryption |

## Enabling Encryption

1. Generate a 32-byte random key and base64-encode it:
```Bash
head -c 32 /dev/urandom | base64
```
2. Create the `EncryptionConfiguration` manifest
3. Pass it to the API server: `--encryption-provider-config=/etc/kubernetes/encryption-config.yaml`
4. Restart the API server (modify the static pod manifest in `/etc/kubernetes/manifests/`)
5. Re-encrypt existing Secrets: `kubectl get secrets -A -o json | kubectl replace -f -`
