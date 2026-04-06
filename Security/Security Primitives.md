
Security primitives are the built-in mechanisms and objects that help secure a Kubernetes cluster, control access, and protect workloads

## Authentication 

| Authentication Primitive                                                         | Purpose |
| -------------------------------------------------------------------------------- | ------- |
| **X.509 Certificates**                                                           |         |
| **Bearer tokens**                                                                |         |
| **[[Service Accounts\|ServiceAccounts]]** |         |
| **External Providers**                                                           |         |

## Authorization 

| Authentication Primitive | Purpose |
| ------------------------ | ------- |
| **RBAC**                 |         |
| **ABAC**                 |         |
| **Webhook Mode**         |         |

## Admission Control 

| Admission Control Primitive          | Purpose |
| ------------------------------------ | ------- |
| **Mutating Admission Controllers**   |         |
| **Validating Admission Controllers** |         |

## Network Security 

| Network Security Primitive | Purpose |
| -------------------------- | ------- |
| **[[Network Policies]]**    |         |

## Secrets Management

| Secret Management Primitive                                              | Purpose |
| ------------------------------------------------------------------------ | ------- |
| **[[kubernetesVault/Objects/ServiceAccounts/Secrets/Secrets\|Secrets]]** |         |

## Pod Security

| Pod Security Primitive     | Purpose |
| -------------------------- | ------- |
| **Pod Security Admission** |         |

## Container Security

| Container Security Primitive | Purpose |
| ---------------------------- | ------- |
| **securityContext**          |         |
