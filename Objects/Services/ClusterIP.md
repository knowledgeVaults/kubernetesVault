
```YAML
apiVersion: v1
kind: Service
metadata:
  name:
spec:
  type: ClusterIP
  ports:
    - name:
      targetPort: CONTAINER_PORT
      port: SERVICE_PORT
  selector:
    KEY: VALUE
```