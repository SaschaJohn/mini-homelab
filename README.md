# Getting started

## Create cluster

```shell
scp veh@192.168.1.100:/home/veh/.kube/config ~/.kube/config
```

Add Gateway API CRDs

```shell
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/experimental-install.yaml
```

Add Cilium

```shell
kubectl kustomize --enable-helm infra/cilium | kubectl apply -f -
```

Add Sealed Secrets

```shell
kubectl kustomize --enable-helm infra/sealed-secrets | kubectl apply -f -
```

Add Cert-manager

```shell
kubectl kustomize --enable-helm infra/cert-manager | kubectl apply -f -
```

Get CF API token and create secret

```shell
CF_API_TOKEN=<CF_API_TOKEN>
kubectl -n gateway create secret generic cloudflare-api-token --from-literal=api-token=${CF_API_TOKEN} --dry-run=client -oyaml > cf-api-secret.yaml 
```

Seal secret

```shell
kubeseal -o yaml < cf-api-secret.yaml > infra/gateway/cloudflare-api-token.yaml
```

Gateway

```shell
kubectl kustomize --enable-helm infra/gateway | kubectl apply -f -
```

Cloudflare tunnel

```shell
cloudflared tunnel create <NAME>
```

```shell
TUNNEL_CREDENTIALS=<CREDENTIALS_FILE>
kubectl -n cloudflared create secret generic tunnel-credentials --from-literal=credentials.json=$(cat ${TUNNEL_CREDENTIALS}) --dry-run=client -oyaml > tunnel-secret.yaml 
```

Seal secret

```shell
kubeseal -o yaml < tunnel-secret.yaml > infra/cloudflared/tunnel-credentials.yaml
```

```shell
kubectl apply -k infra/cloudflared
```

Get Tunnel ID (not Connector ID) and add DNS CNAME record with `<id>.cfargotunnel.com`

Add Argo CD

```shell
kubectl kustomize --enable-helm infra/argocd | kubectl apply -f -
```

Get Argo CD admin secret

```shell
kubectl -n argocd get secret argocd-initial-admin-secret -ojson | jq -r ' .data.password | @base64d'
```

Apply app-of-apps

```shell
kubectl apply -k sets
```