
* Helps keep track of application versions after every modification to help enable rollbacks to previous versions if necessary

Get the history of deployments
```Bash
kubectl rollout history deployment/DEPLOYMENT_NAME -n NAMESPACE
```