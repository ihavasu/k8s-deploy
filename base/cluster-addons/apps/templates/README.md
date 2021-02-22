```
APPLICATION=argocd-traefik envsubst < generic.yaml.tmpl > $APPLICATION.yaml
APPLICATION=external-dns envsubst < generic.yaml.tmpl > $APPLICATION.yaml
APPLICATION=traefik envsubst < generic.yaml.tmpl > $APPLICATION.yaml
```
