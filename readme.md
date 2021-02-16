# How to get a cluster working in Kind with ingress

## Assumptions
- Kind is installed
- Docker is installed
- Kubernetes for Docker on Desktop is not running
- Tilt is installed
## Create Cluster

```
cat <<EOF | kind create cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF
```

## Add an Ingress Solution 
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

## Start all Services, Deployments and Ingress
```
tilt up
```

## Clean up
```
tilt down
kind delete cluster
```

# Appendix

## Source
https://kind.sigs.k8s.io/docs/user/ingress/
