
```YAML
apiVersion: v1
kind: Service
metadata:
  name: 
spec:
  type: NodePort
  ports:
    - name: 
      targetPort: CONTAINER_PORT
      port: SERVICE_PORT
      nodePort: NODE_PORT
  selector:
    KEY: LABEL
```