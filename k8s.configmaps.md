# Índice ejemplos

 - [Crear ConfigMap a partir de un archivo de texto](#crear-configMap-a-partir-de-un-archivo-de-texto)
 - [Crear ConfigMap a partir de un directorio](#crear-configmap-a-partir-de-un-directorio)
 - [Recuperar manifest](#recuperar-manifest)
 - [Cambiar key](#cambiar-key)
 - [Crear ConfigMap desde environment file](#crear-configmap-desde-environment-file)
 - [Crear literalmente](#crear-literalmente)
 - [Crear config map con manifest](#crear-config-map-con-manifest)
 - [Leer como directorio](#leer-como-directorio)
 - [Leer como environment variables](#leer-como-environment-variables)

``` powershell
kubectl get configmap
```

#### Crear ConfigMap a partir de un archivo de texto

file.txt
``` txt
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice
```

``` powershell
kubectl create configmap game-config --from-file=configmap/file.txt
```

``` powershell
kubectl describe configmap game-config
```

#### Crear ConfigMap a partir de un directorio


file2.txt
``` txt
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
```

``` powershell
kubectl create configmap game-config2 --from-file=configmap/
```

``` powershell
kubectl get configmap
kubectl describe configmap
```

##### Recuperar manifest
``` powershell
kubectl get configmaps game-config -o yaml
```

##### Cambiar key
``` powershell
kubectl create configmap game-config3 --from-file=special-key=configmap/file.txt
```

#### Crear ConfigMap desde environment file
env.txt
``` txt
enemies=aliens
lives=3
allowed="true"

# This comment and the empty line above it are ignored
```

``` powershell
kubectl create configmap config-env-file --from-env-file=configmap/env.txt
```

#### Crear literalmente
``` powershell
kubectl create configmap special-config --from-literal=special.how=very --from-literal=special.type=charm
```

``` powershell
kubectl describe configmap special-config
```


#### Crear config map con manifest

``` yml
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    app: myconfigmap
  name: myconfigmap
data:
  MONGODB_DBNAME: mymongo
  MONGODB_PASSWORD: password 
  MONGODB_ROLE: readWrite
  MONGODB_ROOT_PASSWORD: password
  MONGODB_ROOT_ROLE: root
  MONGODB_ROOT_USERNAME: dbadmin
  MONGODB_USERNAME: webrole
  ```

  ``` powershell
  kubectl apply -f configmap.yml
  ```

### Leer como directorio

  ```yml
  apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    configMap:
      name: myconfigmap
  ```

  ``` powershell
  kubectl apply -f .\pod.cm.yml
  kubectl exec -it mypod --bash
  ```
  ``` bash
  cd etc/foo
  ls -l
  ```

## Leer como environment variables

``` yml
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    app: myconfigmap
  name: app-settings
data:
  MY_KEY1: my_value_1
  PASSWORD: password 
  ROLE: readWrite
  ROOT_PASSWORD: password
  USERNAME: webrole
```

``` powershell
kubectl apply -f cf.yml
```


``` yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-configmap
spec:
  selector:
    matchLabels:
      app: nginx-configmap
  template:
    metadata:
      labels:
        app: nginx-configmap
    spec:
      containers:
      - name: nginx-configmap
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        resources:
        ports:
        - containerPort: 9000

        volumeMounts: # montamos el config map como un volumen para tenerlo accesible en un directorio del container
          - name: app-config-vol
            mountPath: /etc/config

        env: # leemos cada environment variable de a una desde un config map creado
        - name: MY_ENV1
          valueFrom:
            configMapKeyRef:
              name: app-settings
              key: MY_KEY1
              optional: true

        envFrom: # leemos todas las environment variables y las cargamos en el container
        - configMapRef:
            name: app-settings

      volumes: # se crea un volumen a partir de un config map
      - name: app-config-vol
        configMap:
          name: app-settings
```

``` powershell
kubectl apply -f nginx.configmap.yml
kubectl exec -it mynginx.configmap -- bash
```

``` powershell
cd etc/config
ls -l
printenv
```

[Secrets](k8s.secrets.md)