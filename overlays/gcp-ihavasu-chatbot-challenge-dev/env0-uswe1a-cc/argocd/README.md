## Configure GitHub Oauth

```
# Copy the GitHub Client Secret to the pasteboard after you copy-paste this to your command-line then run it
kubectl create secret generic argocd-secret -n argocd --dry-run -o yaml --from-literal=dex.github.clientSecret=$(pbpaste | base64) \
  | kubectl apply -f -
```

## Configure Argo CD to access the GitHub Repo

```
# Copy the SSH key to the pasteboard after you copy-paste this to your command-line then run it
kubectl create secret generic github-repo-deploy-key -n argocd \
  --from-literal=sshPrivateKey=$(pbpaste | base64)
```

### TODO
* sops
