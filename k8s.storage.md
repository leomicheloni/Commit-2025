## Índice de ejemplos

 - [EmptyDir](#emptyDir)
 - [HostPath](#hostpath)
 - [Persisten Volume](#persistent-volume)

#### EmptyDir

``` yml
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx-alpine-volume
spec: 
  containers: 
  - name: nginx 
    image: nginx:alpine 
    volumeMounts: 
      - name: html 
        mountPath: /usr/share/nginx/html
        readOnly: true 
    resources:
  - name: html-updater 
    image: alpine 
    command: ["/bin/sh", "-c"] 
    args: 
      - while true; do date >> /html/index.html;sleep 10; done 
    resources:
    volumeMounts: 
      - name: html 
        mountPath: /html 
  volumes: 
  - name: html 
    emptyDir: {} #lifecycle tied to Pod
```

``` powershell
kubectl port-forward pod/nginx-alpine-volume 8088:80
```


#### HostPath

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
        volumeMounts: # los volúmnes se montan en los contenedores
        - name: hostpath
          mountPath: /usr/share/nginx/html
    volumes: 
      - name: hostpath
        hostPath: # un path en el Nodo, es decir, muere si el nodo desaparece
          path: /tmp
          type: DirectoryOrCreate # puede ser de varios tipos
```

``` powershell
kubectl apply -f hostpath.yml
kubectl cp /html/index.html my-nginx-684b44998b-rkqg7:/usr/share/nginx/html/index.html
```

``` powershell
kubectl get storageclass 
```

### Persistent Volume

``` yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
    type: DirectoryOrCreate #por defecto es Directory, en caso del directorio no existir daría error
```

``` powershell
kubectl apply -f pv.yml
kubectl get pv
```

``` yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

``` powershell
kubectl apply -f pvc.yml
kubectl get pvc
```


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
        resources: # acá pueden ir cosas como límites de CPU y memoria
        volumeMounts:
        - name: vol1
          mountPath: /usr/share/nginx/html        
      volumes:
      - name: vol1
        persistentVolumeClaim:
          claimName: task-pv-claim
```

``` powershell
kubectl apply -f .
```

``` powershell
kubectl get pv
```

``` powershell
kubectl get pvc
```

[ConfigMaps](k8s.configmaps.md)