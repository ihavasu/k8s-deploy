## Enable Dashboard

```
kubectl apply -f dashboard.yaml -n traefik
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name -n traefik) -n traefik 9000:9000
```
