
```YAML
apiVersion: kubescheduler.config.k8s.io/v1
kin: KubeSchedulerConfiguration
profiles:
- schedulerName: SCHEDULER_NAME
  plugins:
    score:
      disabled:
      - name: PLUGIN_NAME
      enabled:
      - name: PLUGIN_NAME
```