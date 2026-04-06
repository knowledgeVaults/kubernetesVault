ConfigMaps store non-confidential configuration data either key-value pairs or binary data in order to decouple configuration from container images

* No built-in encryption
* Injected into pods either via environment variables, volumes, or command-line arguments
* Keys must be alphanumeric

## ConfigMap Definition 

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: OBJECT_NAME
data: 
  KEY: "VALUE" 
binaryData: 
  ca-cert.pem: LS0tLS1CRUdJTi... # Base64-encoded binary cert file app-icon.png: iVBORw0KGgoAAAANS... # Base64-encoded image
```

* `data:` stores plaintext key-value pairs
* `binaryData:` stores base64-encoded non-text values (*certs*, *images*, *etc.*)