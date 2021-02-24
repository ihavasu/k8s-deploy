## Installation

install.yaml created by:

```
helm template datadog datadog/datadog \
  -n monitoring \
  --set datadog.site='datadoghq.com' \
  --set datadog.kubelet.tlsVerify=false \
  --set datadog.logs.enabled=true \
  --set datadog.logs.containerCollectAll=true \
  --set datadog.apm.enabled=true \
  --set datadog.dogstatsd.nonLocalTraffic=true \
  --set datadog.dogstatsd.useHostPort=true \
  --set clusterAgent.metricsProvider.enabled=true \
  --set clusterAgent.metricsProvider.useDatadogMetrics=true \
  --set datadog.apiKeyExistingSecret=datadog \
  --set datadog.appKeyExistingSecret=datadog-appkey \
  > install.yaml
```

Manual:
* Update the `datadog/templates/secret-cluster-agent-token.yaml` with the old token if this is an update.
* Update `checksum/clusteragent_token` wherever it is referenced

//TODO:
* Update the token procedure to be secure since we want to use this base on different clusters

### Create api-key Secret

```
kubectl create secret generic datadog -n datadog --dry-run=client -o yaml --from-literal=api-key=$(pbpaste) \
  | kubectl apply -f -
```

### Create app-key Secret

```
kubectl create secret generic datadog-appkey -n datadog --dry-run=client -o yaml --from-literal=app-key=$(pbpaste) \
  | kubectl apply -f -
```


### Future changes might be needed
```
credentials.apiSecret.keyName
credentials.apiSecret.secretName
credentials.appSecret.keyName
credentials.appSecret.secretName
```
