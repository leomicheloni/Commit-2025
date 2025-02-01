# Índice de ejemplos

 - [ClusterIP](#clusterip)
 - [LoadBalancer](#loadbalancer)

#### ClusterIP

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx2
  labels:
    app: my-nginx2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-nginx2
  template:
    metadata:
      labels:
        app: my-nginx2
    spec:
      containers:
      - name: my-nginx2
        image: nginx:alpine
        ports:
        resources:
```

``` powershell
kubectl apply -f .\nginx2.yml
```

``` powershell
kubectl exec -it nginx2 -- bash

curl http://10.1.1.65

kubectl delete pod nginxs

kubectl get pods -o wide
```

``` yml
apiVersion: v1
kind: Service

metadata:
  name: nginx-clusterip ## DNS name (cluster internal)
  labels:
    app: nginx
spec:
  type: ClusterIP
  selector:
    app: my-nginx # hace match con los pods con esa etiqueta app: nginx
  ports:
  - name: http
    port: 8080
    targetPort: 80
```

``` powershell
kubectl apply -f clusterip.yml
kubectl get all -o wide
kubectl exec it
curl http://IP:8080
curl http://dnsname:8080
```
### LoadBalancer

``` yml
apiVersion: v1
kind: Service
metadata:
 name: nginx-loadbalancer
spec:
 type: LoadBalancer
 selector:
    app: my-nginx
 ports:
  - name: "80"
    port: 8080
    targetPort: 80
    
  # - name: "443"
  #   port: 443
  #   targetPort: 443
```
``` powershell
kubectl apply -f loadbalancer.yml
```

``` powershell
kubectl scale deployment.apps/my-nginx  --replicas=1
```

``` powershell
kubectl scale deployment.apps/my-nginx  --replicas=2

kubectl logs pod/my-pod -f

kubectl delete pod my-nginx

kubectl scale deployment.apps/my-nginx  --replicas=1
```

[Storage](k8s.storage.md)