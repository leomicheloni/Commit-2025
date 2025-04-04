# Índice de ejemplos

 - [ClusterIP](#clusterip)
 - [LoadBalancer](#loadbalancer)

#### ClusterIP

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
  labels:
    app: my-nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx:alpine
        ports:
        resources:
```

``` powershell
kubectl apply -f .\deployment.yaml
```

``` powershell
kubectl exec -it nginx -- bash

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
kubectl apply -f loadbalancer.yaml
```

``` powershell
kubectl scale deployment.apps/my-nginx  --replicas=1
```

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 4
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
    spec:
      containers:
        - name: my-nginx
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: html-volume
              mountPath: /usr/share/nginx/html
          command: ["/bin/bash", "-c"]
          args:
            - |
              echo "<!DOCTYPE html>
              <html>
              <head>
                <title>Pod Info</title>
                <script>
                  setTimeout(() => { location.reload(); }, 5000);
                </script>
              </head>
              <body>
                <h1>Welcome to Pod $(hostname)</h1>
              </body>
              </html>" > /usr/share/nginx/html/index.html && nginx -g 'daemon off;'
      volumes:
        - name: html-volume
          emptyDir: {}
```          


``` powershell
kubectl scale deployment.apps/my-nginx  --replicas=2

kubectl logs pod/my-pod -f

kubectl delete pod my-nginx

kubectl scale deployment.apps/my-nginx  --replicas=1
```

[Storage](k8s.storage.md)