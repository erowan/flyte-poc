# Deploy Flyte Sandbox to a k8s local env


This deployment is closer to the real thing as the k3s sandbox runs everything in one container
so you have to docker exec onto the container to run kubectl and inspect the runtime.


I am using mac OSX but Docker Desktop with kubernetes is also available on Windows.

[docker-desktop](https://www.docker.com/products/docker-desktop)


Enable kubernete in Docker UI settings

Note this will delete all resources

[reference](https://docs.docker.com/desktop/kubernetes/)


```
$ brew install kubectl

$ kubectl config set-context docker-desktop
Context "docker-desktop" modified.
$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   119s


$ kubectl config get-contexts
CURRENT   NAME             CLUSTER          AUTHINFO         NAMESPACE
*         docker-desktop   docker-desktop   docker-desktop   flyte
```


## Deploy Flyte sandbox

I was using the ['Docker-Mac + K8s setup'](https://docs.flyte.org/en/latest/deployment/sandbox.html)

but the docs where not complete so I had to fill in the gaps ...


if upgrading delete previous install

```
$ kubectl delete namespaces flyte projectcontour kubernetes-dashboard flyteexamples-development flyteexamples-production flyteexamples-staging
$ kubectl delete customresourcedefinitions extensionservices.projectcontour.io flyteworkflows.flyte.lyft.com httpproxies.projectcontour.io tlscertificatedelegations.projectcontour.io
$ kubectl delete clusterroles flyte-pod-webhook flyteadmin contour flytepropeller kubernetes-dashboard
$ kubectl delete clusterrolebindings flyte-pod-webhook flyteadmin-binding contour flytepropeller kubernetes-dashboard kubernetes-dashboard-admin
```

Note flyte_generated.yaml is a snapshot taken from [flytes github](https://raw.githubusercontent.com/flyteorg/flyte/master/deployment/sandbox/flyte_generated.yaml)

```
$ kubectl create -f flyte_generated.yaml 
namespace/flyte created
namespace/kubernetes-dashboard created
namespace/projectcontour created
customresourcedefinition.apiextensions.k8s.io/extensionservices.projectcontour.io created
customresourcedefinition.apiextensions.k8s.io/httpproxies.projectcontour.io created
customresourcedefinition.apiextensions.k8s.io/tlscertificatedelegations.projectcontour.io created
Warning: apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
customresourcedefinition.apiextensions.k8s.io/flyteworkflows.flyte.lyft.com created
serviceaccount/datacatalog created
serviceaccount/flyte-pod-webhook created
serviceaccount/flyteadmin created
serviceaccount/flytepropeller created
serviceaccount/kubernetes-dashboard created
serviceaccount/contour created
serviceaccount/contour-certgen created
serviceaccount/envoy created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
role.rbac.authorization.k8s.io/contour-certgen created
clusterrole.rbac.authorization.k8s.io/flyte-pod-webhook created
clusterrole.rbac.authorization.k8s.io/flyteadmin created
clusterrole.rbac.authorization.k8s.io/contour created
clusterrole.rbac.authorization.k8s.io/flytepropeller created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/contour created
clusterrolebinding.rbac.authorization.k8s.io/flyte-pod-webhook created
clusterrolebinding.rbac.authorization.k8s.io/contour created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard-admin created
Warning: rbac.authorization.k8s.io/v1beta1 ClusterRoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 ClusterRoleBinding
clusterrolebinding.rbac.authorization.k8s.io/flyteadmin-binding created
clusterrolebinding.rbac.authorization.k8s.io/flytepropeller created
configmap/clusterresource-template-dtg8ff28mt created
configmap/datacatalog-config-64k8dg9gck created
configmap/flyte-admin-config-g49kfbhmdf created
configmap/flyte-console-config created
configmap/flyte-propeller-config-d9bhkt4m5d created
configmap/kubernetes-dashboard-settings created
configmap/contour created
secret/db-pass-9dgchhk2bm created
secret/flyte-admin-auth created
secret/flyte-propeller-auth created
secret/user-info created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
service/datacatalog created
service/flyte-pod-webhook created
service/flyteadmin created
service/flyteconsole created
service/minio created
service/minio-direct created
service/postgres created
service/postgres-direct created
service/dashboard-metrics-scraper created
service/kubernetes-dashboard created
service/contour created
service/envoy created
deployment.apps/datacatalog created
deployment.apps/flyte-pod-webhook created
deployment.apps/flyteadmin created
deployment.apps/flyteconsole created
deployment.apps/flytepropeller created
deployment.apps/minio created
deployment.apps/postgres created
deployment.apps/dashboard-metrics-scraper created
deployment.apps/kubernetes-dashboard created
deployment.apps/contour created
Warning: batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob
cronjob.batch/syncresources created
daemonset.apps/envoy created
job.batch/flyte-pod-webhook-secret created
job.batch/contour-certgen-v1.13.1 created
ingress.networking.k8s.io/flytesystem created
ingress.networking.k8s.io/minio created


$ kubectl get all -n flyte
NAME                                     READY   STATUS              RESTARTS   AGE
pod/datacatalog-7679db76b-dzgnt          1/1     Running             0          2m16s
pod/flyte-pod-webhook-6b7bdd655b-bfl7t   0/1     ContainerCreating   0          2m16s
pod/flyteadmin-7dff4b4d77-vhnvp          2/2     Running             0          2m16s
pod/flyteconsole-66c5575f79-9z6sd        1/1     Running             0          2m16s
pod/flytepropeller-54dc5d7b8f-qz8mj      1/1     Running             0          2m16s
pod/minio-8564dc858c-z9qtz               1/1     Running             0          2m16s
pod/postgres-6c64cd5c48-znb6v            1/1     Running             0          2m16s
pod/syncresources-27087056-j9jq7         0/1     Completed           0          117s
pod/syncresources-27087057-kr4wk         0/1     Completed           0          57s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                AGE
service/datacatalog         ClusterIP   10.103.159.99    <none>        88/TCP,89/TCP          2m16s
service/flyte-pod-webhook   ClusterIP   10.109.10.130    <none>        443/TCP                2m16s
service/flyteadmin          ClusterIP   10.104.137.131   <none>        87/TCP,80/TCP,81/TCP   2m16s
service/flyteconsole        ClusterIP   10.110.112.46    <none>        80/TCP                 2m16s
service/minio               ClusterIP   10.100.149.91    <none>        9000/TCP               2m16s
service/minio-direct        NodePort    10.98.156.246    <none>        9000:30084/TCP         2m16s
service/postgres            ClusterIP   10.106.113.11    <none>        5432/TCP               2m16s
service/postgres-direct     NodePort    10.107.34.121    <none>        5432:30083/TCP         2m16s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/datacatalog         1/1     1            1           2m16s
deployment.apps/flyte-pod-webhook   0/1     1            0           2m16s
deployment.apps/flyteadmin          1/1     1            1           2m16s
deployment.apps/flyteconsole        1/1     1            1           2m16s
deployment.apps/flytepropeller      1/1     1            1           2m16s
deployment.apps/minio               1/1     1            1           2m16s
deployment.apps/postgres            1/1     1            1           2m16s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/datacatalog-7679db76b          1         1         1       2m16s
replicaset.apps/flyte-pod-webhook-6b7bdd655b   1         1         0       2m16s
replicaset.apps/flyteadmin-7dff4b4d77          1         1         1       2m16s
replicaset.apps/flyteconsole-66c5575f79        1         1         1       2m16s
replicaset.apps/flytepropeller-54dc5d7b8f      1         1         1       2m16s
replicaset.apps/minio-8564dc858c               1         1         1       2m16s
replicaset.apps/postgres-6c64cd5c48            1         1         1       2m16s

NAME                          SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/syncresources   */1 * * * *   False     0        57s             2m16s

NAME                               COMPLETIONS   DURATION   AGE
job.batch/syncresources-27087056   1/1           77s        117s
job.batch/syncresources-27087057   1/1           20s        57s
```


## What UIs have been deployed?


A Flyte UI at http://localhost:30081/

A K8 dashboard is served at http://localhost:30082/

Minio at http://localhost:30084/minio/

MINIO_ACCESS_KEY:  minio
MINIO_SECRET_KEY:  miniostorage



### Setup Env

```
$ python3 -m venv flyte-dev
$ source flyte-dev/bin/activate
(flyte-dev) $ python
Python 3.7.1 (default, Dec 14 2018, 13:28:58) 
[Clang 4.0.1 (tags/RELEASE_401/final)] :: Anaconda, Inc. on darwin

(flyte-dev) $ pip install --pre flytekit
Collecting flytekit
...
Successfully installed attrs-21.2.0 certifi-2021.5.30 chardet-4.0.0 click-7.1.2 croniter-1.0.15 dataclasses-json-0.5.4 decorator-5.0.9 deprecated-1.2.12 dirhash-0.2.1 docker-image-py-0.1.10 flyteidl-0.19.7 flytekit-0.20.0b2 grpcio-1.38.1 idna-2.10 importlib-metadata-4.6.0 keyring-23.0.1 marshmallow-3.12.1 marshmallow-enum-1.5.1 marshmallow-jsonschema-0.12.0 mypy-extensions-0.4.3 natsort-7.1.1 numpy-1.21.0 pandas-1.3.0rc1 pathspec-0.8.1 protobuf-3.17.3 py-1.10.0 pyarrow-3.0.0 python-dateutil-2.8.1 python-json-logger-2.0.1 pytimeparse-1.1.8 pytz-2018.4 regex-2021.4.4 requests-2.25.1 responses-0.13.3 retry-0.9.2 scantree-0.0.1 six-1.16.0 sortedcontainers-2.4.0 statsd-3.3.0 stringcase-1.2.0 typing-extensions-3.10.0.0 typing-inspect-0.7.1 urllib3-1.25.11 wheel-0.36.2 wrapt-1.12.1 zipp-3.4.1
```

Note: You also need to install flytectl with brew

```
$ brew install flyteorg/homebrew-tap/flytectl

to update

$ brew update && brew upgrade flytectl
==> Upgrading 1 outdated package:
flyteorg/tap/flytectl 0.1.29 -> 0.2.16
==> Upgrading flyteorg/tap/flytectl
  0.1.29 -> 0.2.16 



(flyte-dev) $ flytectl get project
{"json":{},"level":"warning","msg":"Starting an unauthenticated client because: failed to fetch auth metadata. Error: rpc error: code = Unimplemented desc = unknown service flyteidl.service.AuthMetadataService","ts":"2021-07-03T11:19:58+01:00"}
{"json":{},"level":"info","msg":"Initialized Admin client","ts":"2021-07-03T11:19:58+01:00"}
 --------------- --------------- --------------------------- 
| ID            | NAME          | DESCRIPTION               |
 --------------- --------------- --------------------------- 
| flyteexamples | flyteexamples | flyteexamples description |
 --------------- --------------- --------------------------- 
| flytesnacks   | flytesnacks   | flytesnacks description   |
 --------------- --------------- --------------------------- 
2 rows
```


### Build TASK/WORKFLOW IMAGE

```
$ git clone git@github.com:flyteorg/flytesnacks.git flytesnacks
Cloning into 'flytesnacks'...

cd flytesnacks/cookbook/case_studies/ml_training/house_price_prediction


Change the Dockerfile removing the house_price_prediction dir from these lines

# Install Python dependencies
COPY requirements.txt /root
COPY sandbox.config /root
# Copy the actual code
COPY ./ /root/house_price_prediction/

(flyte-dev) $ docker build . --tag house_price_prediction:dev
```


### Register a new project

```
 (flyte-dev) $ flyte-cli register-project -i -h localhost:30081 -p myflyteproject --name "My Flyte Project" --description "My very first project onboarding onto Flyte"
Config file not found at default location, relying on environment variables instead.
                        To setup your config file run 'flyte-cli setup-config'
Welcome to Flyte CLI! Version: 0.20.0b2
Registered project [id: myflyteproject, name: My Flyte Project, description: My very first project onboarding onto Flyte]

OR

curl -X POST localhost:30081/api/v1/projects -d '{"project": {"id": "myflyteproject", "name": "myflyteproject"} }'
```

Note Running serialize/register in the built container instead of locally as I had issues installing xgboost on OSX

Overriding the entrypoint in the image which is ENTRYPOINT ["/usr/local/bin/flytekit_venv"]

```
(flyte-dev) $ docker run -it --entrypoint=/bin/bash house_price_prediction:dev 
root@e9bd8bc9c9f5:~# pwd
/root

root@e9bd8bc9c9f5:~# ls
Makefile  house_price_prediction  requirements.txt  sandbox.config
```

#### Activate virtual env

```
root@e9bd8bc9c9f5:~# source /opt/venv/bin/activate
(venv) root@e9bd8bc9c9f5:~# 
```

#### Running the workflow in the container locally without flyte k8s pod execution

```
(venv) root@e9bd8bc9c9f5:~# python house_price_prediction/house_price_predictor.py 
2021-07-06 12:21:22,886-flytekit-DEBUG$ Task returns named tuple <class '__main__.GenerateSplitDataOutputs'>
2021-07-06 12:21:22,887-flytekit-DEBUG$ Task returns named tuple <class '__main__.Model'>
2021-07-06 12:21:22,888-flytekit-DEBUG$ Task returns unnamed native tuple typing.List[float]
2021-07-06 12:21:22,889-flytekit-DEBUG$ Task returns unnamed native tuple typing.List[float]
2021-07-06 12:21:22,892-flytekit-INFO$ Invoking __main__.generate_and_split_data with inputs: {'number_of_houses': 1000, 'seed': 7}
INFO:flytekit:Invoking __main__.generate_and_split_data with inputs: {'number_of_houses': 1000, 'seed': 7}
2021-07-06 12:21:22,991-flytekit-INFO$ Task executed successfully in user level, outputs: (        PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    146650.0      1985.0       1469.0           4.0            3.0       1.42            0.0
1    355000.0      1989.0       2898.0           2.0            2.0       1.02            1.0
2    451900.0      2006.0       2691.0           4.0            2.5       1.05            2.0
3    697150.0      2007.0       4455.0           2.0            2.0       1.26            2.0
4    482200.0      1980.0       3528.0           5.0            3.0       1.20            3.0
..        ...         ...          ...           ...            ...        ...            ...
595  543500.0      2004.0       3315.0           6.0            1.0       0.75            3.0
596  339100.0      1993.0       2597.0           3.0            3.0       0.97            0.0
597  589500.0      2003.0       3585.0           6.0            3.0       1.45            1.0
598  471250.0      1987.0       3480.0           6.0            2.0       0.95            1.0
599  369350.0      1989.0       3033.0           3.0            2.0       0.96            0.0

[600 rows x 7 columns],         PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    388850.0      1988.0       3002.0           2.0            2.0       1.57            2.0
1    284950.0      1978.0       2549.0           6.0            1.5       0.34            2.0
2     78350.0      1983.0       1153.0           3.0            3.0       1.36            0.0
3    721700.0      1990.0       5170.0           4.0            2.0       1.08            1.0
4    453400.0      1985.0       3268.0           4.0            2.5       1.38            3.0
..        ...         ...          ...           ...            ...        ...            ...
295  343450.0      1998.0       2377.0           5.0            2.5       0.96            0.0
296  440000.0      1994.0       3282.0           2.0            2.5       0.68            1.0
297  518750.0      1988.0       3687.0           5.0            2.5       0.88            2.0
298  476400.0      1987.0       3597.0           5.0            1.0       0.79            2.0
299  551350.0      1985.0       4132.0           4.0            2.5       1.27            1.0

[300 rows x 7 columns],        PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0   426550.0      1986.0       3417.0           2.0            1.5       1.10            2.0
1   507600.0      2003.0       3045.0           3.0            3.0       1.39            3.0
2   392550.0      2009.0       2477.0           3.0            1.0       1.40            1.0
3   262350.0      1982.0       2307.0           6.0            2.5       0.92            0.0
4   426800.0      2001.0       2439.0           6.0            3.0       0.73            3.0
..       ...         ...          ...           ...            ...        ...            ...
95  563200.0      1981.0       4260.0           6.0            2.0       1.28            1.0
96  542400.0      1996.0       3628.0           5.0            2.5       1.38            1.0
97  344900.0      2007.0       1855.0           3.0            3.0       1.11            3.0
98  262650.0      1988.0       1996.0           6.0            1.5       1.05            2.0
99  470950.0      1990.0       3550.0           6.0            1.5       0.73            0.0

[100 rows x 7 columns])
INFO:flytekit:Task executed successfully in user level, outputs: (        PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    146650.0      1985.0       1469.0           4.0            3.0       1.42            0.0
1    355000.0      1989.0       2898.0           2.0            2.0       1.02            1.0
2    451900.0      2006.0       2691.0           4.0            2.5       1.05            2.0
3    697150.0      2007.0       4455.0           2.0            2.0       1.26            2.0
4    482200.0      1980.0       3528.0           5.0            3.0       1.20            3.0
..        ...         ...          ...           ...            ...        ...            ...
595  543500.0      2004.0       3315.0           6.0            1.0       0.75            3.0
596  339100.0      1993.0       2597.0           3.0            3.0       0.97            0.0
597  589500.0      2003.0       3585.0           6.0            3.0       1.45            1.0
598  471250.0      1987.0       3480.0           6.0            2.0       0.95            1.0
599  369350.0      1989.0       3033.0           3.0            2.0       0.96            0.0

[600 rows x 7 columns],         PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    388850.0      1988.0       3002.0           2.0            2.0       1.57            2.0
1    284950.0      1978.0       2549.0           6.0            1.5       0.34            2.0
2     78350.0      1983.0       1153.0           3.0            3.0       1.36            0.0
3    721700.0      1990.0       5170.0           4.0            2.0       1.08            1.0
4    453400.0      1985.0       3268.0           4.0            2.5       1.38            3.0
..        ...         ...          ...           ...            ...        ...            ...
295  343450.0      1998.0       2377.0           5.0            2.5       0.96            0.0
296  440000.0      1994.0       3282.0           2.0            2.5       0.68            1.0
297  518750.0      1988.0       3687.0           5.0            2.5       0.88            2.0
298  476400.0      1987.0       3597.0           5.0            1.0       0.79            2.0
299  551350.0      1985.0       4132.0           4.0            2.5       1.27            1.0

[300 rows x 7 columns],        PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0   426550.0      1986.0       3417.0           2.0            1.5       1.10            2.0
1   507600.0      2003.0       3045.0           3.0            3.0       1.39            3.0
2   392550.0      2009.0       2477.0           3.0            1.0       1.40            1.0
3   262350.0      1982.0       2307.0           6.0            2.5       0.92            0.0
4   426800.0      2001.0       2439.0           6.0            3.0       0.73            3.0
..       ...         ...          ...           ...            ...        ...            ...
95  563200.0      1981.0       4260.0           6.0            2.0       1.28            1.0
96  542400.0      1996.0       3628.0           5.0            2.5       1.38            1.0
97  344900.0      2007.0       1855.0           3.0            3.0       1.11            3.0
98  262650.0      1988.0       1996.0           6.0            1.5       1.05            2.0
99  470950.0      1990.0       3550.0           6.0            1.5       0.73            0.0

[100 rows x 7 columns])
2021-07-06 12:21:23,089-flytekit-INFO$ Invoking __main__.fit with inputs: {'loc': 'NewYork_NY', 'train':         PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    146650.0      1985.0       1469.0           4.0            3.0       1.42            0.0
1    355000.0      1989.0       2898.0           2.0            2.0       1.02            1.0
2    451900.0      2006.0       2691.0           4.0            2.5       1.05            2.0
3    697150.0      2007.0       4455.0           2.0            2.0       1.26            2.0
4    482200.0      1980.0       3528.0           5.0            3.0       1.20            3.0
..        ...         ...          ...           ...            ...        ...            ...
595  543500.0      2004.0       3315.0           6.0            1.0       0.75            3.0
596  339100.0      1993.0       2597.0           3.0            3.0       0.97            0.0
597  589500.0      2003.0       3585.0           6.0            3.0       1.45            1.0
598  471250.0      1987.0       3480.0           6.0            2.0       0.95            1.0
599  369350.0      1989.0       3033.0           3.0            2.0       0.96            0.0

[600 rows x 7 columns], 'val':         PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    388850.0      1988.0       3002.0           2.0            2.0       1.57            2.0
1    284950.0      1978.0       2549.0           6.0            1.5       0.34            2.0
2     78350.0      1983.0       1153.0           3.0            3.0       1.36            0.0
3    721700.0      1990.0       5170.0           4.0            2.0       1.08            1.0
4    453400.0      1985.0       3268.0           4.0            2.5       1.38            3.0
..        ...         ...          ...           ...            ...        ...            ...
295  343450.0      1998.0       2377.0           5.0            2.5       0.96            0.0
296  440000.0      1994.0       3282.0           2.0            2.5       0.68            1.0
297  518750.0      1988.0       3687.0           5.0            2.5       0.88            2.0
298  476400.0      1987.0       3597.0           5.0            1.0       0.79            2.0
299  551350.0      1985.0       4132.0           4.0            2.5       1.27            1.0

[300 rows x 7 columns]}
INFO:flytekit:Invoking __main__.fit with inputs: {'loc': 'NewYork_NY', 'train':         PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    146650.0      1985.0       1469.0           4.0            3.0       1.42            0.0
1    355000.0      1989.0       2898.0           2.0            2.0       1.02            1.0
2    451900.0      2006.0       2691.0           4.0            2.5       1.05            2.0
3    697150.0      2007.0       4455.0           2.0            2.0       1.26            2.0
4    482200.0      1980.0       3528.0           5.0            3.0       1.20            3.0
..        ...         ...          ...           ...            ...        ...            ...
595  543500.0      2004.0       3315.0           6.0            1.0       0.75            3.0
596  339100.0      1993.0       2597.0           3.0            3.0       0.97            0.0
597  589500.0      2003.0       3585.0           6.0            3.0       1.45            1.0
598  471250.0      1987.0       3480.0           6.0            2.0       0.95            1.0
599  369350.0      1989.0       3033.0           3.0            2.0       0.96            0.0

[600 rows x 7 columns], 'val':         PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0    388850.0      1988.0       3002.0           2.0            2.0       1.57            2.0
1    284950.0      1978.0       2549.0           6.0            1.5       0.34            2.0
2     78350.0      1983.0       1153.0           3.0            3.0       1.36            0.0
3    721700.0      1990.0       5170.0           4.0            2.0       1.08            1.0
4    453400.0      1985.0       3268.0           4.0            2.5       1.38            3.0
..        ...         ...          ...           ...            ...        ...            ...
295  343450.0      1998.0       2377.0           5.0            2.5       0.96            0.0
296  440000.0      1994.0       3282.0           2.0            2.5       0.68            1.0
297  518750.0      1988.0       3687.0           5.0            2.5       0.88            2.0
298  476400.0      1987.0       3597.0           5.0            1.0       0.79            2.0
299  551350.0      1985.0       4132.0           4.0            2.5       1.27            1.0

[300 rows x 7 columns]}
[0]	validation_0-rmse:313785.12500
[1]	validation_0-rmse:224384.75000
[2]	validation_0-rmse:162146.39062
[3]	validation_0-rmse:118533.48438
[4]	validation_0-rmse:87461.64062
[5]	validation_0-rmse:66076.82031
[6]	validation_0-rmse:51560.24219
[7]	validation_0-rmse:41510.38672
[8]	validation_0-rmse:34881.19141
[9]	validation_0-rmse:30854.30078
[10]	validation_0-rmse:28070.94922
[11]	validation_0-rmse:26142.47852
[12]	validation_0-rmse:24945.94141
[13]	validation_0-rmse:24337.34375
[14]	validation_0-rmse:23641.44727
[15]	validation_0-rmse:23369.42969
[16]	validation_0-rmse:22970.91211
[17]	validation_0-rmse:22739.54492
[18]	validation_0-rmse:22568.91992
[19]	validation_0-rmse:22391.89844
[20]	validation_0-rmse:22228.91016
[21]	validation_0-rmse:22067.76172
[22]	validation_0-rmse:21921.02539
[23]	validation_0-rmse:21783.19531
[24]	validation_0-rmse:21759.40625
[25]	validation_0-rmse:21692.10352
[26]	validation_0-rmse:21625.78906
[27]	validation_0-rmse:21535.50781
[28]	validation_0-rmse:21466.89648
[29]	validation_0-rmse:21397.10547
[30]	validation_0-rmse:21296.67773
[31]	validation_0-rmse:21259.37305
[32]	validation_0-rmse:21170.45117
[33]	validation_0-rmse:21156.62500
[34]	validation_0-rmse:21134.10547
[35]	validation_0-rmse:21107.40234
[36]	validation_0-rmse:21027.51172
[37]	validation_0-rmse:21022.23828
[38]	validation_0-rmse:20996.85742
[39]	validation_0-rmse:20982.94531
[40]	validation_0-rmse:20944.06445
[41]	validation_0-rmse:20913.19922
[42]	validation_0-rmse:20899.43750
[43]	validation_0-rmse:20890.88281
[44]	validation_0-rmse:20878.42773
[45]	validation_0-rmse:20875.53711
[46]	validation_0-rmse:20904.72852
[47]	validation_0-rmse:20902.69336
[48]	validation_0-rmse:20891.38867
[49]	validation_0-rmse:20892.36133
[50]	validation_0-rmse:20879.10938
[51]	validation_0-rmse:20868.15625
[52]	validation_0-rmse:20833.62695
[53]	validation_0-rmse:20829.95117
[54]	validation_0-rmse:20832.87305
[55]	validation_0-rmse:20826.09766
[56]	validation_0-rmse:20828.49609
[57]	validation_0-rmse:20829.63281
[58]	validation_0-rmse:20819.05273
[59]	validation_0-rmse:20806.27539
[60]	validation_0-rmse:20788.35547
[61]	validation_0-rmse:20786.70703
[62]	validation_0-rmse:20784.65039
[63]	validation_0-rmse:20781.23633
[64]	validation_0-rmse:20786.17188
[65]	validation_0-rmse:20789.96484
[66]	validation_0-rmse:20790.45703
[67]	validation_0-rmse:20787.54102
[68]	validation_0-rmse:20787.22852
[69]	validation_0-rmse:20782.94727
[70]	validation_0-rmse:20790.04297
[71]	validation_0-rmse:20789.05664
[72]	validation_0-rmse:20763.07617
[73]	validation_0-rmse:20763.20312
[74]	validation_0-rmse:20768.13867
[75]	validation_0-rmse:20762.26562
[76]	validation_0-rmse:20763.26758
[77]	validation_0-rmse:20763.47461
[78]	validation_0-rmse:20760.47656
[79]	validation_0-rmse:20757.57617
[80]	validation_0-rmse:20750.87695
[81]	validation_0-rmse:20748.62695
[82]	validation_0-rmse:20746.04492
[83]	validation_0-rmse:20743.79102
[84]	validation_0-rmse:20742.48438
[85]	validation_0-rmse:20737.42188
[86]	validation_0-rmse:20735.67969
[87]	validation_0-rmse:20728.57227
[88]	validation_0-rmse:20726.26172
[89]	validation_0-rmse:20727.32227
[90]	validation_0-rmse:20725.42383
[91]	validation_0-rmse:20724.09961
[92]	validation_0-rmse:20724.34180
[93]	validation_0-rmse:20725.06641
[94]	validation_0-rmse:20722.14453
[95]	validation_0-rmse:20721.72461
[96]	validation_0-rmse:20720.54297
[97]	validation_0-rmse:20719.88281
[98]	validation_0-rmse:20725.88281
[99]	validation_0-rmse:20720.58008
2021-07-06 12:21:23,300-flytekit-INFO$ Task executed successfully in user level, outputs: model-NewYork_NY.joblib.dat
INFO:flytekit:Task executed successfully in user level, outputs: model-NewYork_NY.joblib.dat
2021-07-06 12:21:23,325-flytekit-INFO$ Invoking __main__.predict with inputs: {'test':        PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0   426550.0      1986.0       3417.0           2.0            1.5       1.10            2.0
1   507600.0      2003.0       3045.0           3.0            3.0       1.39            3.0
2   392550.0      2009.0       2477.0           3.0            1.0       1.40            1.0
3   262350.0      1982.0       2307.0           6.0            2.5       0.92            0.0
4   426800.0      2001.0       2439.0           6.0            3.0       0.73            3.0
..       ...         ...          ...           ...            ...        ...            ...
95  563200.0      1981.0       4260.0           6.0            2.0       1.28            1.0
96  542400.0      1996.0       3628.0           5.0            2.5       1.38            1.0
97  344900.0      2007.0       1855.0           3.0            3.0       1.11            3.0
98  262650.0      1988.0       1996.0           6.0            1.5       1.05            2.0
99  470950.0      1990.0       3550.0           6.0            1.5       0.73            0.0

[100 rows x 7 columns], 'model_ser': /tmp/flyte/20210706_122122/mock_remote/33e8f21060fa7cc423ea077c8767c30d/model-NewYork_NY.joblib.dat}
INFO:flytekit:Invoking __main__.predict with inputs: {'test':        PRICE  YEAR_BUILT  SQUARE_FEET  NUM_BEDROOMS  NUM_BATHROOMS  LOT_ACRES  GARAGE_SPACES
0   426550.0      1986.0       3417.0           2.0            1.5       1.10            2.0
1   507600.0      2003.0       3045.0           3.0            3.0       1.39            3.0
2   392550.0      2009.0       2477.0           3.0            1.0       1.40            1.0
3   262350.0      1982.0       2307.0           6.0            2.5       0.92            0.0
4   426800.0      2001.0       2439.0           6.0            3.0       0.73            3.0
..       ...         ...          ...           ...            ...        ...            ...
95  563200.0      1981.0       4260.0           6.0            2.0       1.28            1.0
96  542400.0      1996.0       3628.0           5.0            2.5       1.38            1.0
97  344900.0      2007.0       1855.0           3.0            3.0       1.11            3.0
98  262650.0      1988.0       1996.0           6.0            1.5       1.05            2.0
99  470950.0      1990.0       3550.0           6.0            1.5       0.73            0.0

[100 rows x 7 columns], 'model_ser': /tmp/flyte/20210706_122122/mock_remote/33e8f21060fa7cc423ea077c8767c30d/model-NewYork_NY.joblib.dat}
2021-07-06 12:21:23,346-flytekit-INFO$ Task executed successfully in user level, outputs: [428397.0, 486687.4375, 402570.0, 241104.65625, 400617.40625, 411560.9375, 334282.78125, 366538.5, 559682.75, 426885.375, 411110.53125, 413846.0, 522257.21875, 364194.0, 301331.625, 502869.90625, 522544.46875, 484730.125, 262344.46875, 565652.625, 152387.75, 481774.78125, 581078.0, 524254.34375, 313889.78125, 463703.4375, 213016.203125, 535191.375, 586328.8125, 528858.9375, 397277.03125, 482509.0, 418422.65625, 477509.15625, 313576.9375, 440548.53125, 305791.28125, 314925.34375, 333805.03125, 678046.5, 413732.5625, 467082.625, 467785.9375, 473361.6875, 274566.96875, 421138.21875, 217376.4375, 475949.09375, 111706.984375, 301830.25, 574423.375, 463440.6875, 373479.125, 495740.59375, 281860.625, 371542.625, 557873.3125, 580810.0, 414522.21875, 370215.90625, 341049.5625, 306748.84375, 228799.015625, 449677.8125, 616481.875, 437929.15625, 316782.65625, 380658.8125, 285673.09375, 634019.125, 377730.8125, 576798.5, 323117.09375, 372071.5, 352913.15625, 425237.21875, 675146.9375, 487457.6875, 305738.21875, 336066.8125, 333265.5, 509260.78125, 601568.0, 365937.90625, 447272.84375, 189271.4375, 367539.0, 374243.09375, 424794.28125, 584933.1875, 440066.75, 170445.234375, 224244.59375, 508162.21875, 552780.9375, 572432.5, 555501.25, 324170.28125, 260092.578125, 452722.40625]
INFO:flytekit:Task executed successfully in user level, outputs: [428397.0, 486687.4375, 402570.0, 241104.65625, 400617.40625, 411560.9375, 334282.78125, 366538.5, 559682.75, 426885.375, 411110.53125, 413846.0, 522257.21875, 364194.0, 301331.625, 502869.90625, 522544.46875, 484730.125, 262344.46875, 565652.625, 152387.75, 481774.78125, 581078.0, 524254.34375, 313889.78125, 463703.4375, 213016.203125, 535191.375, 586328.8125, 528858.9375, 397277.03125, 482509.0, 418422.65625, 477509.15625, 313576.9375, 440548.53125, 305791.28125, 314925.34375, 333805.03125, 678046.5, 413732.5625, 467082.625, 467785.9375, 473361.6875, 274566.96875, 421138.21875, 217376.4375, 475949.09375, 111706.984375, 301830.25, 574423.375, 463440.6875, 373479.125, 495740.59375, 281860.625, 371542.625, 557873.3125, 580810.0, 414522.21875, 370215.90625, 341049.5625, 306748.84375, 228799.015625, 449677.8125, 616481.875, 437929.15625, 316782.65625, 380658.8125, 285673.09375, 634019.125, 377730.8125, 576798.5, 323117.09375, 372071.5, 352913.15625, 425237.21875, 675146.9375, 487457.6875, 305738.21875, 336066.8125, 333265.5, 509260.78125, 601568.0, 365937.90625, 447272.84375, 189271.4375, 367539.0, 374243.09375, 424794.28125, 584933.1875, 440066.75, 170445.234375, 224244.59375, 508162.21875, 552780.9375, 572432.5, 555501.25, 324170.28125, 260092.578125, 452722.40625]
[428397.0, 486687.4375, 402570.0, 241104.65625, 400617.40625, 411560.9375, 334282.78125, 366538.5, 559682.75, 426885.375, 411110.53125, 413846.0, 522257.21875, 364194.0, 301331.625, 502869.90625, 522544.46875, 484730.125, 262344.46875, 565652.625, 152387.75, 481774.78125, 581078.0, 524254.34375, 313889.78125, 463703.4375, 213016.203125, 535191.375, 586328.8125, 528858.9375, 397277.03125, 482509.0, 418422.65625, 477509.15625, 313576.9375, 440548.53125, 305791.28125, 314925.34375, 333805.03125, 678046.5, 413732.5625, 467082.625, 467785.9375, 473361.6875, 274566.96875, 421138.21875, 217376.4375, 475949.09375, 111706.984375, 301830.25, 574423.375, 463440.6875, 373479.125, 495740.59375, 281860.625, 371542.625, 557873.3125, 580810.0, 414522.21875, 370215.90625, 341049.5625, 306748.84375, 228799.015625, 449677.8125, 616481.875, 437929.15625, 316782.65625, 380658.8125, 285673.09375, 634019.125, 377730.8125, 576798.5, 323117.09375, 372071.5, 352913.15625, 425237.21875, 675146.9375, 487457.6875, 305738.21875, 336066.8125, 333265.5, 509260.78125, 601568.0, 365937.90625, 447272.84375, 189271.4375, 367539.0, 374243.09375, 424794.28125, 584933.1875, 440066.75, 170445.234375, 224244.59375, 508162.21875, 552780.9375, 572432.5, 555501.25, 324170.28125, 260092.578125, 452722.40625]
(venv) root@e9bd8bc9c9f5:~# 
```


###  Serialize tasks + workflows

```
(venv) root@df2de999a1a6:~# mkdir _pb_output
(venv) root@df2de999a1a6:~# pyflyte -c sandbox.config --pkgs house_price_prediction serialize --in-container-config-path /root/flyte.config --image house_price_prediction:dev workflows -f _pb_output
Using configuration file at /root/sandbox.config
Flyte Admin URL None
Serializing Flyte elements with image house_price_prediction:dev
Writing output to _pb_output
No images specified, will use the default image
DEBUG:root:		[2] Pushing context - execute, branch[False], StackOrigin(serialize_all, 163, /opt/venv/lib/python3.8/site-packages/flytekit/clis/sdk_in_container/serialize.py)
2021-07-05 19:25:19,671-flytekit-DEBUG$ Task returns named tuple <class 'house_price_prediction.house_price_predictor.GenerateSplitDataOutputs'>
DEBUG:flytekit:Task returns named tuple <class 'house_price_prediction.house_price_predictor.GenerateSplitDataOutputs'>
2021-07-05 19:25:19,671-flytekit-DEBUG$ Task returns named tuple <class 'house_price_prediction.house_price_predictor.Model'>
DEBUG:flytekit:Task returns named tuple <class 'house_price_prediction.house_price_predictor.Model'>
2021-07-05 19:25:19,672-flytekit-DEBUG$ Task returns unnamed native tuple typing.List[float]
DEBUG:flytekit:Task returns unnamed native tuple typing.List[float]
2021-07-05 19:25:19,673-flytekit-DEBUG$ Task returns unnamed native tuple typing.List[float]
DEBUG:flytekit:Task returns unnamed native tuple typing.List[float]
DEBUG:root:			[3] Pushing context - compile, branch[False], StackOrigin(compile, 619, /opt/venv/lib/python3.8/site-packages/flytekit/core/workflow.py)
DEBUG:root:			[3] Popping context - compile, branch[False], StackOrigin(compile, 619, /opt/venv/lib/python3.8/site-packages/flytekit/core/workflow.py)
2021-07-05 19:25:19,677-flytekit-DEBUG$ Task returns named tuple <class 'house_price_prediction.multiregion_house_price_predictor.GenerateSplitDataOutputs'>
DEBUG:flytekit:Task returns named tuple <class 'house_price_prediction.multiregion_house_price_predictor.GenerateSplitDataOutputs'>
2021-07-05 19:25:19,678-flytekit-DEBUG$ Task returns unnamed native tuple typing.List[typing.List[float]]
DEBUG:flytekit:Task returns unnamed native tuple typing.List[typing.List[float]]
2021-07-05 19:25:19,679-flytekit-DEBUG$ Task returns unnamed native tuple typing.List[typing.List[float]]
DEBUG:flytekit:Task returns unnamed native tuple typing.List[typing.List[float]]
DEBUG:root:			[3] Pushing context - compile, branch[False], StackOrigin(compile, 619, /opt/venv/lib/python3.8/site-packages/flytekit/core/workflow.py)
DEBUG:root:			[3] Popping context - compile, branch[False], StackOrigin(compile, 619, /opt/venv/lib/python3.8/site-packages/flytekit/core/workflow.py)
Found 7 tasks/workflows
DEBUG:root:Looking for LHS for <flytekit.core.python_auto_container.DefaultTaskResolver object at 0x7ff8c9a76370> from flytekit.core.python_auto_container
DEBUG:root:Found LHS for <flytekit.core.python_auto_container.DefaultTaskResolver object at 0x7ff8c9a76370>, default_task_resolver
  Writing to file: 0_house_price_prediction.house_price_predictor.generate_and_split_data_1.pb
  Writing to file: 1_house_price_prediction.house_price_predictor.fit_1.pb
  Writing to file: 2_house_price_prediction.house_price_predictor.predict_1.pb
  Writing to file: 3_house_price_prediction.house_price_predictor.house_price_predictor_trainer_2.pb
  Writing to file: 4_house_price_prediction.house_price_predictor.house_price_predictor_trainer_3.pb
  Writing to file: 5_house_price_prediction.multiregion_house_price_predictor.generate_and_split_data_multiloc_1.pb
  Writing to file: 6_house_price_prediction.multiregion_house_price_predictor.parallel_fit_predict_1.pb
  Writing to file: 7_house_price_prediction.multiregion_house_price_predictor.multi_region_house_price_prediction_model_trainer_2.pb
  Writing to file: 8_house_price_prediction.multiregion_house_price_predictor.multi_region_house_price_prediction_model_trainer_3.pb
Successfully serialized 9 flyte objects
DEBUG:root:		[2] Popping context - execute, branch[False], StackOrigin(serialize_all, 163, /opt/venv/lib/python3.8/site-packages/flytekit/clis/sdk_in_container/serialize.py)

(venv) root@df2de999a1a6:~# ls _pb_output/
0_house_price_prediction.house_price_predictor.generate_and_split_data_1.pb        5_house_price_prediction.multiregion_house_price_predictor.generate_and_split_data_multiloc_1.pb
1_house_price_prediction.house_price_predictor.fit_1.pb                            6_house_price_prediction.multiregion_house_price_predictor.parallel_fit_predict_1.pb
2_house_price_prediction.house_price_predictor.predict_1.pb                        7_house_price_prediction.multiregion_house_price_predictor.multi_region_house_price_prediction_model_trainer_2.pb
3_house_price_prediction.house_price_predictor.house_price_predictor_trainer_2.pb  8_house_price_prediction.multiregion_house_price_predictor.multi_region_house_price_prediction_model_trainer_3.pb
4_house_price_prediction.house_price_predictor.house_price_predictor_trainer_3.pb
```

### Registration


Install flytectl

```
(venv) root@df2de999a1a6:~# curl -s https://raw.githubusercontent.com/lyft/flytectl/master/install.sh | bash
flyteorg/flytectl info checking GitHub for latest tag
flyteorg/flytectl info found version: 0.2.1 for v0.2.1/Linux/x86_64
flyteorg/flytectl info installed ./bin/flytectl

(venv) root@e9bd8bc9c9f5:~# export PATH=$PATH:$HOME/bin

(venv) root@df2de999a1a6:~# cd _pb_output/

(venv) root@df2de999a1a6:~# tar -cvf myproject.tar *.pb
(venv) root@df2de999a1a6:~# cd ..
```



If the docker container is unable to connect to the flye admin service
Outside the docker container find out what the network bridge gateway is

```
$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "d7fcea97cc8bc86fda6d034ab9e2efa70c64732abb4b9f402cc075d6a1c3afd1",
        "Created": "2021-07-02T10:40:58.769859285Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "df2de999a1a6e985a82cc0845f1a2ab85f55310d82f7ae5a192b647d25973b81": {
                "Name": "cool_cannon",
                "EndpointID": "4c5a3af98177f60bbaee616cbd7a0502251a298ad02b60200a4b889727246b01",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

Update $HOME/.flyte/config.yaml in the container with the value of IPAM.Config.Gateway which is 172.17.0.1 here


mkdir -p $HOME/.flyte
cat << EOF > $HOME/.flyte/config.yaml
admin:
  # For GRPC endpoints you might want to use dns:///flyte.myexample.com
  endpoint: dns:///172.17.0.1:30081
  insecure: true
  authType: Pkce # if using authentication or just drop this. If insecure set insecure: True
storage:
  connection:
    access-key: minio
    auth-type: accesskey
    disable-ssl: true
    endpoint: http://172.17.0.1:30084
    region: my-region-here
    secret-key: miniostorage
  container: my-s3-bucket
  type: minio
EOF

Check you can connect to the admin server? 

(venv) root@df2de999a1a6:~# curl -v telnet://172.17.0.1:30081
*   Trying 172.17.0.1:30081...
* TCP_NODELAY set
* Connected to 172.17.0.1 (172.17.0.1) port 30081 (#0)


(venv) root@df2de999a1a6:~# ./bin/flytectl register files -p 'myflyteproject' -d development --archive ./_pb_output/myproject.tar  --version v

{"json":{},"level":"warning","msg":"Starting an unauthenticated client because: failed to fetch auth metadata. Error: rpc error: code = Unimplemented desc = unknown service flyteidl.service.AuthMetadataService","ts":"2021-07-06T08:58:57Z"}
{"json":{},"level":"info","msg":"Initialized Admin client","ts":"2021-07-06T08:58:57Z"}
{"json":{},"level":"info","msg":"Parsing file... Total(9)","ts":"2021-07-06T08:58:57Z"}
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| NAME (9)                                                                                                                                 | STATUS  | ADDITIONAL INFO              |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/0_house_price_prediction.house_price_predictor.generate_and_split_data_1.pb                                       | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/1_house_price_prediction.house_price_predictor.fit_1.pb                                                           | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/2_house_price_prediction.house_price_predictor.predict_1.pb                                                       | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/3_house_price_prediction.house_price_predictor.house_price_predictor_trainer_2.pb                                 | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/4_house_price_prediction.house_price_predictor.house_price_predictor_trainer_3.pb                                 | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/5_house_price_prediction.multiregion_house_price_predictor.generate_and_split_data_multiloc_1.pb                  | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/6_house_price_prediction.multiregion_house_price_predictor.parallel_fit_predict_1.pb                              | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/7_house_price_prediction.multiregion_house_price_predictor.multi_region_house_price_prediction_model_trainer_2.pb | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
| /tmp/register528318444/8_house_price_prediction.multiregion_house_price_predictor.multi_region_house_price_prediction_model_trainer_3.pb | Success | Successfully registered file |
 ------------------------------------------------------------------------------------------------------------------------------------------ --------- ------------------------------ 
9 rows
```

You should now see 2 workflows in the [flight console](http://localhost:30081/console/projects/myflyteproject/workflows)

Launch house_price_predictor_trainer

What execution pods were created?

```
$ kubectl get namespaces
NAME                         STATUS   AGE
default                      Active   4d1h
flyte                        Active   4d1h
flyteexamples-development    Active   4d1h
flyteexamples-production     Active   4d1h
flyteexamples-staging        Active   4d1h
flytesnacks-development      Active   4d1h
flytesnacks-production       Active   4d1h
flytesnacks-staging          Active   4d1h
kube-node-lease              Active   4d1h
kube-public                  Active   4d1h
kube-system                  Active   4d1h
kubernetes-dashboard         Active   4d1h
myflyteproject-development   Active   23h
myflyteproject-production    Active   23h
myflyteproject-staging       Active   23h
projectcontour               Active   4d1h


$ kubectl get all -n myflyteproject-development
NAME                  READY   STATUS      RESTARTS   AGE
pod/bahyicye9e-n0-0   0/1     Completed   0          14m
pod/bahyicye9e-n1-0   0/1     Completed   0          13m
pod/bahyicye9e-n2-0   0/1     Completed   0          12m
pod/h7fff0k23f-n0-0   0/1     Completed   0          26m
```

What does the pod look like?

```
$ kubectl describe po bahyicye9e-n0-0  -n myflyteproject-development
Name:         bahyicye9e-n0-0
Namespace:    myflyteproject-development
Priority:     0
Node:         docker-desktop/192.168.65.4
Start Time:   Tue, 06 Jul 2021 13:11:51 +0100
Labels:       execution-id=bahyicye9e
              interruptible=false
              node-id=n0
              task-name=house-price-prediction-house-price-predictor-generate-and-split
              workflow-name=house-price-prediction-house-price-predictor-house-price-predic
Annotations:  cluster-autoscaler.kubernetes.io/safe-to-evict: false
Status:       Succeeded
IP:           10.1.6.234
IPs:
  IP:           10.1.6.234
Controlled By:  flyteworkflow/bahyicye9e
Containers:
  bahyicye9e-n0-0:
    Container ID:  docker://43c1f8638de7da97778d2c7bcb0e6fda8e86d2c7969473dffbc12f81e00b4406
    Image:         house_price_prediction:dev
    Image ID:      docker://sha256:d5d622f7006bd2069b908c1d690c9b624efdcac0aa8e4620fdcf01bda8ceb5c4
    Port:          <none>
    Host Port:     <none>
    Args:
      pyflyte-execute
      --inputs
      s3://my-s3-bucket/metadata/propeller/myflyteproject-development-bahyicye9e/n0/data/inputs.pb
      --output-prefix
      s3://my-s3-bucket/metadata/propeller/myflyteproject-development-bahyicye9e/n0/data/0
      --raw-output-data-prefix
      s3://my-s3-bucket/9z/bahyicye9e-n0-0
      --resolver
      flytekit.core.python_auto_container.default_task_resolver
      --
      task-module
      house_price_prediction.house_price_predictor
      task-name
      generate_and_split_data
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Tue, 06 Jul 2021 13:11:55 +0100
      Finished:     Tue, 06 Jul 2021 13:12:47 +0100
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     100m
      memory:  600Mi
    Requests:
      cpu:     100m
      memory:  200Mi
    Environment:
      FLYTE_INTERNAL_CONFIGURATION_PATH:  /root/flyte.config
      FLYTE_INTERNAL_IMAGE:               house_price_prediction:dev
      FLYTE_INTERNAL_EXECUTION_WORKFLOW:  myflyteproject:development:house_price_prediction.house_price_predictor.house_price_predictor_trainer
      FLYTE_INTERNAL_EXECUTION_ID:        bahyicye9e
      FLYTE_INTERNAL_EXECUTION_PROJECT:   myflyteproject
      FLYTE_INTERNAL_EXECUTION_DOMAIN:    development
      FLYTE_ATTEMPT_NUMBER:               0
      FLYTE_INTERNAL_TASK_PROJECT:        myflyteproject
      FLYTE_INTERNAL_TASK_DOMAIN:         development
      FLYTE_INTERNAL_TASK_NAME:           house_price_prediction.house_price_predictor.generate_and_split_data
      FLYTE_INTERNAL_TASK_VERSION:        v1
      FLYTE_INTERNAL_PROJECT:             myflyteproject
      FLYTE_INTERNAL_DOMAIN:              development
      FLYTE_INTERNAL_NAME:                house_price_prediction.house_price_predictor.generate_and_split_data
      FLYTE_INTERNAL_VERSION:             v1
      FLYTE_AWS_ENDPOINT:                 http://minio.flyte:9000
      FLYTE_AWS_ACCESS_KEY_ID:            minio
      FLYTE_AWS_SECRET_ACCESS_KEY:        miniostorage
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-d96c5 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-d96c5:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  15m   default-scheduler  Successfully assigned myflyteproject-development/bahyicye9e-n0-0 to docker-desktop
  Normal  Pulled     15m   kubelet            Container image "house_price_prediction:dev" already present on machine
  Normal  Created    15m   kubelet            Created container bahyicye9e-n0-0
  Normal  Started    15m   kubelet            Started container bahyicye9e-n0-0


The custom workflow resources

$ kubectl get flyteworkflows -n myflyteproject-development
NAME         AGE
bahyicye9e   4h59m
h7fff0k23f   5h11m


$ kubectl get flyteworkflow bahyicye9e -n myflyteproject-development
NAME         AGE
bahyicye9e   5h
Rowans-MacBook-Pro:flyte-poc rowan$ kubectl describe flyteworkflow bahyicye9e -n myflyteproject-development
Name:         bahyicye9e
Namespace:    myflyteproject-development
Labels:       execution-id=bahyicye9e
              hour-of-day=12
              termination-status=terminated
              workflow-name=house-price-prediction-house-price-predictor-house-price-predic
Annotations:  <none>
Accepted At:  2021-07-06T12:11:45Z
API Version:  flyte.lyft.com/v1alpha1
Execution Config:
  Max Parallelism:    0
  Task Plugin Impls:  <nil>
Execution Id:
  Domain:   development
  Name:     bahyicye9e
  Project:  myflyteproject
Inputs:
  Literals:
    number_of_houses:
      Scalar:
        Primitive:
          Integer:  1000
    Seed:
      Scalar:
        Primitive:
          Integer:  7
Kind:               FlyteWorkflow
Metadata:
  Creation Timestamp:  2021-07-06T12:11:46Z
  Generation:          25
  Managed Fields:
    API Version:  flyte.lyft.com/v1alpha1
  ...
```    
