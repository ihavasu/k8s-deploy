```
cd templates
APPLICATION=argocd-traefik envsubst < generic.yaml.tmpl > argocd-traefik.yaml
APPLICATION=external-dns envsubst < generic.yaml.tmpl > external-dns.yaml
APPLICATION=traefik envsubst < generic.yaml.tmpl > traefik.yaml
```

### TODO

Fix up this shortcut template

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ${APPLICATION}
  namespace: argocd
  finalizers:
  - resources-finalizer.argocd.argoproj.io
spec:
  destination:
    namespace: ${APPLICATION}
    server: {{ .Values.spec.destination.server }}
  project: default
  source:
    path: {{ .Values.spec.source.path }}/${APPLICATION}
    repoURL: {{ .Values.spec.source.repoURL }}
    targetRevision: {{ .Values.spec.source.targetRevision }}
```
