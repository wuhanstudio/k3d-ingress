
#### 1. Install Docker
#### 2. Install k3d
#### 3. Create a k3s cluster using k3d

sudo privileges are required because the ingress controller runs on port 80.

```
$ k3d cluster create --network host
```

#### 4. Create cert-manager

```
$ kubectl create namespace cert-manager
$ kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v0.11.0/cert-manager.yaml
$ kubectl get pods --namespace cert-manager
```

#### 4. Apply Configurations


```
$ kubectl create namespace k3d-demo

# Replace example.com with your domain
$ find . -type f -name "*.yml" -exec sed -i'' -e 's/example.com/wuhanstudio.uk/g' {} +

# Replace your@email.com with your email
find . -type f -name "*.yml" -exec sed -i'' -e 's/your@email.com/wuhanstudio@qq.com/g' {} +

$ kubectl apply -f .
```

You should see eveything working.

```
$ kubectl get all -n k3d-demo
NAME                                         READY   STATUS    RESTARTS   AGE
pod/k3d-whoami-deployment-59b799bcf4-scjgb   1/1     Running   0          3m49s
pod/k3d-demo-deployment-5dfdc6488d-vr54z     1/1     Running   0          3m49s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/k3d-demo     ClusterIP   10.43.82.61    <none>        80/TCP    3m49s
service/k3d-whoami   ClusterIP   10.43.51.148   <none>        80/TCP    3m49s

NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/k3d-whoami-deployment   1/1     1            1           3m49s
deployment.apps/k3d-demo-deployment     1/1     1            1           3m49s

NAME                                               DESIRED   CURRENT   READY   AGE
replicaset.apps/k3d-whoami-deployment-59b799bcf4   1         1         1       3m49s
replicaset.apps/k3d-demo-deployment-5dfdc6488d     1         1         1       3m49s
```

Now, we can test the ingress controller.
  
```
$ kubectl get ingress -n k3d-demo
NAME    CLASS    HOSTS                                        ADDRESS         PORTS   AGE
nginx   <none>   nginx.wuhanstudio.uk,whoami.wuhanstudio.uk   172.31.53.219   80      5m3s
```
  
Different domains should serve different web apps, and of course, they are accessible from the public ip address.

```
curl --header "Host: nginx.wuhanstudio.uk"  172.31.53.219
curl --header "Host: whoami.wuhanstudio.uk"  172.31.53.219
```
