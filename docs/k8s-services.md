## Services

```
$ kubectl get svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                AGE
datacatalog         ClusterIP   10.103.159.99    <none>        88/TCP,89/TCP          4d23h
flyte-pod-webhook   ClusterIP   10.109.10.130    <none>        443/TCP                4d23h
flyteadmin          ClusterIP   10.104.137.131   <none>        87/TCP,80/TCP,81/TCP   4d23h
flyteconsole        ClusterIP   10.110.112.46    <none>        80/TCP                 4d23h
minio               ClusterIP   10.100.149.91    <none>        9000/TCP               4d23h
minio-direct        NodePort    10.98.156.246    <none>        9000:30084/TCP         4d23h
postgres            ClusterIP   10.106.113.11    <none>        5432/TCP               4d23h
postgres-direct     NodePort    10.107.34.121    <none>        5432:30083/TCP         4d23h
```

## What UIs have been deployed?

A Flyte UI at http://localhost:30081/console

A K8 dashboard is served at http://localhost:30082/

Minio at http://localhost:30084/minio/

MINIO_ACCESS_KEY:  minio
MINIO_SECRET_KEY:  miniostorage

A contour ingress is used to route to these services
```
$ kubectl get -n projectcontour service envoy -o wide
NAME    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE   SELECTOR
envoy   NodePort   10.111.185.231   <none>        80:30081/TCP,443:31384/TCP   43h   app=envoy
```