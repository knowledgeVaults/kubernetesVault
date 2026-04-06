
## Authorization Techniques
### Node Authorizer

### ABAC

### RBAC

### Webhook

#### Open Policy Agent

## Authorization Modes

* Set using the `--authorization-mode=` flag in the kube-apiserver service (Set to AlwaysAllow by default if no modes are specified)
	* The authorization mode is used in the order that it's specified (ex: *--authorization-mode=Node,RBAC,Webhook*)

### AlwaysAllow

### AlwaysDeny