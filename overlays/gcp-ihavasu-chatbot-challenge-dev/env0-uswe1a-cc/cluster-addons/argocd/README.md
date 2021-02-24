## Configure GitHub Oauth

```
# Copy the GitHub Client Secret to the pasteboard after you copy-paste this to your command-line then run it
# Argo CD expects the secret to be base64 encoded
kubectl create secret generic argocd-secret -n argocd --dry-run=client -o yaml --from-literal=dex.github.clientSecret=$(pbpaste) \
  | kubectl apply -f -
```

## Configure Argo CD to access the GitHub Repo

sshPrivateKey must be PEM encoded

```
# covert openssh private key to pem (basically just reset password and save as pem)
ssh-keygen -p -f ~/.ssh/id_rsa -m pem
```

```
kubectl create secret generic github-repo-deploy-key -n argocd \
  --from-file=sshPrivateKey=/absolute/path/to/id_rsa
```

### TODO
* sops
