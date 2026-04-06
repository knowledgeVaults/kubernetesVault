
* Used to guarantee trust between two parties during a transaction
* Ensures that the encryption between two parties are encrypted and that the two parties are indeed who they say they are
* Uses asymmetric encryption to decrypt and encrypt data (Private and public keys)
* certificate/public keys (.crt or .pem) and private keys (.key or .pem and usually has the word key in the file or certificate name)

```Bash
openssl genrsa -out NAME.key 1024 # NAME.key
openssl rsa -in NAME.key -pubout > NAME.pem # NAME.pem

ssh-keygen
# Creates two files: id_rsa id_rsa.pub
# id_rsa is the private key
# id_rsa.pub is the public key
# The same private key can be used to SSH into multiple servers (Just add the same public key to the servers)

# Login using private key
ssh -i PATH_TO_PRIVATE_KEY USER@SERVER

# ~/.ssh/authorized_keys stores the public keys using the following format: ssh-rsa PUBLIC_KEY USER
```

* Entities can request a certificate to ensure that the other party is who they say they are
* PKI

Certificate data

* Always check who signed and issued the certificate
* Self-signed certificates are not safe (Only trusted when signed by CAs that the browser trusts and that have their own private and public key pairs where their public keys are build in the browsers)
* You can also host your own private CAs internally for applications within an organization
```
Certificate:
  Data:
    Serial Number:
    Signature Algorithm:
    Issuer:
    Validity:
      Not After:
    Subject:
    Subject Alternative Name:
      DNS:
    Subject Public Key Info:
```

```Bash
openssl req -new -key NAME.key -out NAME.csr -subj "VALUE" CN="VALUE"
```

* All servers (*kube-apiserver*, *etcd server*, *kubelet server*, *etc.*) require server certificates and all clients (*kubectl REST API*, *kube-scheduler*, *kube-controller-manager*, *kube-proxy*, *etc.*) require client certificates to verify who they say they are
* Kubernetes requires at least one CA for the cluster (You can have one CA per types of servers such as one CA for client certificates, one for server certificates, and one for client certificates)
* Instead of using usernames and passwords in API calls, you can use certificates (ex: `curl https://kube-apiserver:6443/api/v1/pods --key NAME.key --cert NAME.crt --cacert ca.crt`)
* All servers in a Kubernetes cluster require a copy of the root CA's certificate

| Certificate Type        | Description | Purpose |
| ----------------------- | ----------- | ------- |
| **Root Certificates**   |             |         |
| **Server Certificates** |             |         |
| **Client Certificates** |             |         |

## Generating Certificates

* Some tools include: easyrsa, openssl, cfssl

Generate CA's private and public key (certificate file)
```Bash
# 1. Create a private key
openssl genrsa -out ca.key 2048

# 2. Generate a CSR
openssl req -new -key ca.key -subj "/CN=kube-apiserver" -out ca.csr [-config openssl.cnf]

# 3. Sign the certificates
openssl x509 -req -in ca.cst -signkey ca.key -out ca.crt
```

openssl.cof
```
[req]

[ v3_req ]

[alt_names]
```

Generate private key and certificate signed by the CA
```Bash
# 1. Generate keys
openssl genrsa -out NAME.key 2048

# 2. Generate a CSR
openssl req -new -key NAME.key -subj "/CN=?/OU=?" -out NAME.csr

# 3. Generate a signed certificate using the CA key pair
openssl x509 -req -in NAME.csr -CA ca.crt -CAkey ca.key -out NAME.crt
```

## View Certificates



```Bash
openssl x509 -in CRT_FILE_PATH -text -noout
```

```Bash
journalctl -u NAME.service -l
```

```Bash
crictl ps -a
crictl logs CONTAINER_ID
```

## Certificates API 

```Bash
# 1. Generate keys

# 2. Generate CSR

# 3. Send CSR to admin

# 4. Admin creates a CSR Object

# 5. Approve the CSR
kuebctl certificate approve CSR_NAME
```

* The controller-manager is responsible for carrying out all certificate related operations (It has CSR-APPROVING and CSR-SIGNING controllers that are responsible for carrying out the specific tasks)
* The kube-controller-manager requires the CA's root certificate and private key in order to sign certificates
```YAML
[...]
spec:
  containers:
  - command:
    - kube-controller-manager
      [...]
      - --cluster-signing-cert-file=
      - --cluster-signing-key-file=
```

CSR object
```YAML
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: CSR_NAME
spec:
  signerName: 
  expirationSeconds: SECONDS
  usages:
    - VALUE
  request: BASE64_ENCODED_CSR
  conditions:
  - type: Approved
    status: "True"
    reason: KubectlApprove
    message: "This CSR was approved by kubectl certificate approve."
    lastUpdateTime: "TIME"
```

```Bash
# Get CSR
kubectl get csr

# Get certificate
kubectl get csr CSR_NAME -o yaml # Look for base64 encoded string under 'certificates:'
echo "BASE64_ENCODED_STRING" | base64 --decode
```