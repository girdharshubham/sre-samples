# Teleport

# Enable kind load balancer controller
```
kind create cluster --config ./control-plane.yaml

kind create cluster --config ./data-plane.yaml 

kubectx kind-control-plane
helm repo add teleport https://charts.releases.teleport.dev
helm repo update

go install sigs.k8s.io/cloud-provider-kind@latest && $GOBIN/cloud-provider-kind

helm install teleport teleport/teleport-cluster \
  --namespace teleport \
  --values values.yml --create-namespace

kubectl exec -i -n teleport deployment/teleport-auth -- tctl create -f < member.yaml
kubectl exec -ti -n teleport deployment/teleport-auth -- tctl users add admin --roles=admin,access,editor

# go through the auth flow and sign in

tsh login --proxy=teleport.example.com --user=admin --insecure
tsh kube login teleport.example.com --insecure --proxy teleport.example.com
```