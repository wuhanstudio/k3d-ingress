
1. Install Docker
2. Install k3d
3. Create cluster

```
k3d cluster create --network host
```

4. Apply

```
kubectl create namespace k3d-demo
kubectl apply -f .
kubectl get all -n k3d-demo
```

