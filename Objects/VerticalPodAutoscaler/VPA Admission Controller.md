
* Intervenes the Pod creation process and uses the recommendations from the Recommender to then mutate the Pods to apply the new memory and CPU values at startup
* Ensures that the Pods start with the correct resources limits and requests

```
VPA Recommender --> VPA Updater --> Delete Pods --> VPA Admissioon Controller --> Intercepts Pod Creations --> Vertically Scaled Pods
```