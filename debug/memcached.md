
- Memcached
```bash
$ kubectl get memcached -o wide
NAME               AGE
memcached-sample   3m10s
```

```bash
$ kubectl get po
NAME                                                     READY   STATUS    RESTARTS   AGE
memcached-operator-controller-manager-58cf5df8f6-lbbqq   2/2     Running   0          29m
memcached-sample-9b765dfc8-lgnwd                         1/1     Running   0          16s
memcached-sample-9b765dfc8-rzhw6                         1/1     Running   0          16s
memcached-sample-9b765dfc8-strrt                         1/1     Running   0          16s
```


- Cleanup

```bash
$ kubectl delete -f config/samples/cache_v1alpha1_memcached_1.yaml 
memcached.cache.tan.com "memcached-sample" deleted

$ kubectl get memcached
No resources found in default namespace.

$ kubectl get po
NAME                                                     READY   STATUS    RESTARTS   AGE
memcached-operator-controller-manager-58cf5df8f6-lbbqq   2/2     Running   0          39m

$ kubectl get all
NAME                                                         READY   STATUS    RESTARTS   AGE
pod/memcached-operator-controller-manager-58cf5df8f6-lbbqq   2/2     Running   0          40m

NAME                                                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/kubernetes                                              ClusterIP   10.96.0.1        <none>        443/TCP    20h
service/memcached-operator-controller-manager-metrics-service   ClusterIP   10.104.190.107   <none>        8443/TCP   13h

NAME                                                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/memcached-operator-controller-manager   1/1     1            1           13h

NAME                                                               DESIRED   CURRENT   READY   AGE
replicaset.apps/memcached-operator-controller-manager-58cf5df8f6   1         1         1       40m
replicaset.apps/memcached-operator-controller-manager-69bdff9765   0         0         0       13h


$ kubectl delete deployments,service -l control-plane=controller-manager
deployment.apps "memcached-operator-controller-manager" deleted
service "memcached-operator-controller-manager-metrics-service" deleted


$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   20h


$ k get role
NAME                                      CREATED AT
memcached-operator-leader-election-role   2020-08-29T12:20:00Z


$ k get rolebinding
NAME                                             ROLE                                           AGE
memcached-operator-leader-election-rolebinding   Role/memcached-operator-leader-election-role   18h


$ k delete role,rolebinding --all
role.rbac.authorization.k8s.io "memcached-operator-leader-election-role" deleted
rolebinding.rbac.authorization.k8s.io "memcached-operator-leader-election-rolebinding" deleted

```

