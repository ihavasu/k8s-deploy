### Deploy Argo CD

```
# Login to your Kubernetes cluster
gcloud container clusters get-credentials env0-uswe1a-cc --zone us-west1-a

cd k8s-deploy/overlays/gcp-ihavasu-chatbot-challenge-dev/env0-uswe1a-cc/cluster-addons/argocd
kubectl create ns argocd
kustomize build . | kubectl apply -f -
```

### Configure Secrets

#### GitHub Oauth

```
# Copy the GitHub Client Secret to the pasteboard after you copy-paste this to your command-line then run it
# Argo CD expects the secret to be base64 encoded
kubectl create secret generic argocd-secret -n argocd --dry-run=client -o yaml --from-literal=dex.github.clientSecret=$(pbpaste) \
  | kubectl apply -f -
```

#### GitHub Repo Deploy Key

```
kubectl create secret generic github-repo-deploy-key -n argocd \
  --from-file=sshPrivateKey=/absolute/path/to/id_rsa
```

## Bootstrap Cluster

### Datadog

```
kubectl create ns datadog
```

#### Create api-key Secret

```
kubectl create secret generic datadog -n monitoring --dry-run=client -o yaml --from-literal=api-key=$(pbpaste) \
  | kubectl apply -f -
```

### Create app-key Secret

```
kubectl create secret generic datadog-appkey -n monitoring --dry-run=client -o yaml --from-literal=app-key=$(pbpaste) \
  | kubectl apply -f -
```

### Deploy Cluster Add-ons

One-shot apply to create the app-of-apps Application that installs the cluster-addons.  This will also create the resources that gives you the Argo CD UI with TLS.

```
cd k8s-deploy/overlays/gcp-ihavasu-chatbot-challenge-dev/env0-uswe1a-cc/cluster-addons/apps
kustomize build . | kubectl apply -f -

# Check the status of applications
kubectl get applications -A
```

### Argo CD CLI

After cluster-addons are deployed, you will be able to login to the UI or GRPC.

```
brew install argocd
argocd login argocd.cc-dev-gcp.ihavasu.com --name cc-dev-gcp --sso
```

## Deploy Chatbot

### Install

For development purposes, we will deploy the service, ingress, and HPA but leave the deployment to our skaffold config.

```
cd infra-live/gcp-ihavasu-chatbot-challenge-dev/us-west1/env0/gke-clusters/chatbot-challenge
kubectl apply -f application.yaml -n argocd
```

### Deploy Development

```
cd chatbot-challenge
skaffold dev
```

[chatbot.cc-dev-gcp.ihavasu.com](http://chatbot.cc-dev-gcp.ihavasu.com)

### Load Testing

```
npm install -g artillery

cat <<EOF >300.yaml
config:
  target: "ws://chatbot.cc-dev-gcp.ihavasu.com"
  phases:
    - duration: 300
      arrivalRate: 100
scenarios:
  - engine: "ws"
    flow:
      - send: "help"
      - think: 1
      - send: "remind me to think1 in 5 minutes"
      - think: 1
      - send: "remind me to think2 in 5 minutes"
      - think: 1
      - send: "remind me to think3 in 5 minutes"
      - think: 2
      - send: "show all reminders"
EOF

artillery run 300.yaml
```

### Monitor HPA

```
watch  "kubectl describe hpa chatbot -n chatbot "
```
