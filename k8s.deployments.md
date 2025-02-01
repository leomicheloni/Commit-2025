# Índice de ejemplos

- [Simple](#deployment-simple)
- [Escalar](#escalar)
- [Zero downtime](#zero-downtime)

### deployment simple

``` yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
  labels:
    app: my-nginx #importante para relacionar objetos
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-nginx #objetos a los que afecta
  template:
    metadata:
      labels:
        app: my-nginx #etiqueta custom para asociar este objeto
    spec: # definición de los Pods
      containers:
      - name: my-nginx
        image: nginx:alpine
        ports:
        - containerPort: 80 #solo informativo
        resources: # acá pueden ir cosas como límites de CPU y memoria
```

``` powershell
kubectl apply -f deployment.yml
```

``` powershell
kubectl get deployments
```

``` powershell
kubectl get all
```

### Escalar


``` powershell
kubectl get pods --watch
```

``` powershell
kubectl scale -f deployment.yml --replicas=6
```

``` powershell
kubectl delete pod/my-nginx-576c8b6488-zlh4c
kubectl get pods
```

``` powershell
kubectl scale -f deployment.yml --replicas=2
```
### Zero downtime

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
  labels:
    app: my-nginx #importante para relacionar objetos
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-nginx #objetos a los que afecta
  template:
    metadata:
      labels:
        app: my-nginx #etiqueta custom para asociar este objeto
    spec: # definición de los Pods
      containers:
      - name: my-nginx
        image: nginx:latest # se cambia la imagen
        ports:
        - containerPort: 80 #solo informativo
        resources: # acá pueden ir cosas como límites de CPU y memoria
```

``` powershell
kubectl get pods --watch
```

``` powershell
kubectl apply -f deployment.yml
```

``` powershell
kubectl port-forward deployment.apps/my-nginx 8080:80
```

``` powershell
kubectl delete -f deployment.yml
```


[Services](k8s.services.md)