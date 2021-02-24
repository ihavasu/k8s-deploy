# Generate install.yaml

```
cd generate-install
kustomize build . > ../install.yaml
```

The install.yaml will use the namespace `argocd` which can be overridden in the overlay.
