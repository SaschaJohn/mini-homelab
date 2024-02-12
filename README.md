# Getting started

```shell
scp veh@192.168.1.25:/home/veh/.kube/config ~/.kube/config
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

Get CF API token

Gateway

```shell
kubectl kustomize --enable-helm infra/gateway | kubectl apply -f -
```

```shell
cloudflared tunnel create <NAME>
```
```shell
kubectl create secret generic tunnel-credentials --from-file=/Users/hagen/.cloudflared/<UUID>.json --dry-run=client -oyaml > secret.yaml 
kubeseal -o yaml < secret.yaml > tunnel-credentials.yaml
```

```shell
kubectl apply -k infra/cloudflared
```

Get Tunnel ID (not Connector ID) and add DNS CNAME record with `<id>.cfargotunnel.com`

```shell
kubectl kustomize --enable-helm infra/argocd | kubectl apply -f -
```

Apply app-of-apps
```shell
kubectl apply -k sets
```