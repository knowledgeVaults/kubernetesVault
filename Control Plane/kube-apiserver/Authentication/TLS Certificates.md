
A **TLS certificate** is a digital certificate that verifies a server’s identity and enables encrypted communication over HTTPS

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

* All servers (*[[kube-apiserver]]*, *[[etcd]] server*, *[[kubelet]] server*, *etc.*) require server certificates and all clients (*[[kubectl]] REST API*, *[[kube-scheduler]]*, *[[kube-controller-manager]]*, *[[kube-proxy]]*, *etc.*) require client certificates to verify who they say they are
* Kubernetes requires at least one CA for the cluster (You can have one CA per types of servers such as one CA for client certificates, one for server certificates, and one for client certificates)
* Instead of using usernames and passwords in API calls, you can use certificates (ex: `curl https://kube-apiserver:6443/api/v1/pods --key NAME.key --cert NAME.crt --cacert ca.crt`)
* All servers in a Kubernetes cluster require a copy of the root CA's certificate

| Certificate Type        | Description | Purpose |
