# Índice de ejemplos
 - [Crear un pod de modo imperativo](#crear-un-pod-de-modo-imperativo)
 - [YAML](#yaml)
 - [Probe](#probe)

# PODs

#### Crear un pod de modo imperativo
``` powershell
kubectl run my-nginx --image=nginx:latest
```

``` powershell
kubectl get all -o wide
```

``` powershell
kubectl describe pod my-nginx
```

``` powershell
kubectl logs pod/my-nginx
```

``` powershell
kubectl get pod my-nginx -o yaml
```

#### port forward
``` powershell
kubectl port-forward pod/my-nginx 8080:80
```

``` powershell
kubectl delete pod/my-nginx
```

#### Exec
``` powershell
kubectl run my-nginx --image=nginx:latest
```
``` powershell
kubectl get pod my-nginx -o wide
```

``` powershell
kubectl exec pod/my-nginx -- nginx -v
```

``` powershell
kubectl exec -it pod/my-nginx -- bash
```

> En caso de haber más de un container se puede usar -c, sino se toma el primero

#### Copy
``` powershell
kubectl cp my-nginx:/usr/share/nginx/html/ \html\
```

``` powershell
kubectl delete pod my-nginx
```

## YAML

Es más práctico crear Pods con YAML, si bien, lo más normal es hacerlo mediante deployments

#### Creando un Pod con YAML

Como dijimos la forma más común de crear un Pod es utilizando YAML, para crear un Pod igual que el anterior haríamos esto

``` yaml
apiVersion: v1
kind: Pod
metadata:
 name: my-nginx
 labels:
  app: nginx
  rel: stable
spec:
 containers:
  - name: my-nginx
    image: nginx:alpine
    ports:
     - containerPort: 80 # solo informativo
```

``` powershell
kubectl apply -f mypod.yaml
```

``` powershell
kubectl delete -f mypod.yaml
```

## Probe

``` yaml
apiVersion: v1
kind: Pod
metadata:
 name: my-nginx
 labels:
  app: nginx
  rel: stable
spec:
 containers:
  - name: my-nginx
    image: nginx:alpine
    ports:
     - containerPort: 80
    resources:

    livenessProbe:
     httpGet:
      path: /index.html
      port: 80
     initialDelaySeconds: 15
     timeoutSeconds: 2
     periodSeconds: 5
     failureThreshold: 1
    
    readinessProbe:
     httpGet:
      path: /index.html
      port: 80
     initialDelaySeconds: 15
     periodSeconds: 5
     failureThreshold: 1
```     

``` powershell
kubectl apply -f pod.yml
```

``` powershell
kubectl get pods --watch
```

``` powershell
kubectl logs pods/my-nginx -f
```

Cambiar timeout

```
kubectl apply -f pod.yml
```

[Deployments](k8s.deployments.md)