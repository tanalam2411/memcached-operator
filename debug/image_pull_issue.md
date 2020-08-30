
- Image: https://quay.io/repository/tan24alam11/memcached-operator

- Create Quay.io login secret:

- Generate base64 of docker config
```bash
$ cat ~/.docker/config.json | base64 -w0
```

```bash
$ kubectl create -f quay_secret.yaml
```

```bash
$ kubectl get secrets
NAME                      TYPE                                  DATA   AGE
default-token-gxvk2       kubernetes.io/service-account-token   3      19h
quay-registrypullsecret   kubernetes.io/dockerconfigjson        1      7s
```

```bash
$ kubectl get deploy
NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
memcached-operator-controller-manager   0/1     1            0           12h
```

```bash
$ kubectl get pods
NAME                                                     READY   STATUS              RESTARTS   AGE
memcached-operator-controller-manager-58cf5df8f6-lbbqq   0/2     ContainerCreating   0          14s
memcached-operator-controller-manager-69bdff9765-5hck4   1/2     ImagePullBackOff    0          12h
```

```bash
$ kubectl edit deploy memcached-operator-controller-manager
```


- Add at the same level of containers
```bash
      containers:
      - args:
      ...  
      imagePullSecrets:
      - name: quay-registrypullsecret
```

