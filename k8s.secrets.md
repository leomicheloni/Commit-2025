# Índice de ejemplo

#### Crear secret por comando

``` powershell
kubectl create secret generic empty-secret
kubectl get secret empty-secret
```

``` powershell
kubectl create secret docker-registry secret-tiger-docker \
  --docker-username=tiger \
  --docker-password=pass113 \
  --docker-email=tiger@acme.com
```

#### Basic authentication Secret

  ``` yml
  apiVersion: v1
kind: Secret
metadata:
  name: secret-basic-auth
type: kubernetes.io/basic-auth
stringData:
  username: admin
  password: t0p-Secret
  ```

#### SSH authentication secrets

  ``` yml
  apiVersion: v1
kind: Secret
metadata:
  name: secret-ssh-auth
type: kubernetes.io/ssh-auth
data:
  # the data is abbreviated in this example
  ssh-privatekey: |
          MIIEpQIBAAKCAQEAulqb/Y ...
```

##### Montar secret como volumen a un directorio

``` powershell
kubectl create secret generic db-passwords --from-literal=db-password='password' --from-literal=db-username='dbuser'
```


``` yml
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
    secret:
      secretName: db-passwords
```

``` powershell
kubectl apply -f pod.secret.yml
kubectl exec -it ..
```

```bash
cd /etc/foo
ls -l
```

> es posible actualizar los secrets y sus valores en los volúmnes se actualizan

``` powershell
kubectl edit secrets mysecret
```

#### Utilizar como environment variables

``` yml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: nginx
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: db-passwords
            key: db-username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-passwords
            key: db-password
  restartPolicy: Never
```

``` powershell
echo $SECRET_USERNAME
```

> si el secret cambia se no se actualizan las environment variables