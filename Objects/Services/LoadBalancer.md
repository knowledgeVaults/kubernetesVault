
```YAML
apiVersion: v1
kind: Service
metadata:
  name:
spec:
  type: LoadBalancer
  ports:
    - name:
      targetPort: 
      port:
      nodePort:
  selector:
    KEY: VALUE
```