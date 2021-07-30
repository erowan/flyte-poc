# Flyte POC on GKP

The Flyte POC aims to get Flyte running on GKP with a limited setup.

- Minio S3
- No authentication/authorisation
- Mercury auth bootstrapped in the propeller pod and mercury keys passed via env vars to the project workflow pods. I think the S3 bucket has global scope within a single Flyte deployment currently. This shold be changed to be scoped at the project level to isolate data between different projects. Note the Mecury service does not currently support scoping buckets within a seal app id either yet.  
- The kubernetes-dashboard? Can we run this? It's used to display the k8s task logs.
- CRD customresourcedefinition.apiextensions.k8s.io/flyteworkflows.flyte.lyft.com .
This is a hard requirement and an exception needs to be raised within GKP to allow it. 


## Runtime Components

### Images

cr.flyte.org/flyteorg/flyteadmin

cr.flyte.org/flyteorg/flyteconsole

cr.flyte.org/flyteorg/datacatalog

cr.flyte.org/flyteorg/flytepropeller

tag: flyte-v0.15.0

kubernetesui/dashboard                 v2.2.0  
minio/minio   


### Python client packages

flyteidl               0.19.7   
flytekit               0.20.0b2 

## Namespaces

Flyte creates a namespace 'flyte' for its systems resources

```
(flyte-dev) Rowans-MacBook-Pro:s3 rowan$ kubectl get namespaces
NAME                         STATUS   AGE
flyte                        Active   23h
flyteexamples-development    Active   23h
flyteexamples-production     Active   23h
flyteexamples-staging        Active   23h
...
```

Each project creates 3 namespaces to support a promotion model (dev->staging->prod)

i.e. flyteexamples-development, flyteexamples-staging, flyteexamples-production

A strict namespace naming convention needs to be followed within GKP to support multi-tenancy based on the appid (seal) and resources are bounded by that.

```
<appid>-<project>-<env>
```
Therefore to host Flyte in our GKP dev cluster for CIB Data Science the namespaces would be:

103020-flytepoc-dev

project x workflows

- 103020-flytepoc-x-d-dev
- 103020-flytepoc-x-t-dev
- 103020-flytepoc-x-p-dev

project y workflows

- 103020-flytepoc-y-d-dev
- 103020-flytepoc-y-t-dev
- 103020-flytepoc-y-p-dev

etc

The prefix '103020-flytepoc-' needs to be set within the admin config.

project=x|y

domain=d-dev|t-dev|p-dev


## Build tools

### Helm3

Flyte has both kustomize & helm3 charts which are both deployment options for us. Helm is going to become the defacto at Flyte so it would be best to sync with Helm to avoid our deployment code base getting out of date with the opensource. Best to avoid merging changes from open source helm to jpmc kustomize.


### Local K3D SANDBOX Deployment

The union team are working on a stripped down deployment of flyte for us, removing cluster roles and features not required for the POC.

It's contained in this branch https://github.com/flyteorg/flyte/blob/minimal-perms-jp

See the readme for deployment instructions


```
$ curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash

$ k3d cluster create -p "30081:30081" -p "30084:30084" --no-lb --k3s-server-arg '--no-deploy=traefik' --k3s-server-arg '--no-deploy=servicelb' flyte
WARN[0000] No node filter specified                     
WARN[0000] No node filter specified                     
INFO[0000] Prep: Network                                
INFO[0003] Created network 'k3d-flyte' (3c61aafe2622e3bf005d18c1724a603451be96939b348ecbd11e00f40cae75a3) 
INFO[0003] Created volume 'k3d-flyte-images'            
INFO[0004] Creating node 'k3d-flyte-server-0'           
INFO[0005] Starting cluster 'flyte'                     
INFO[0005] Starting servers...                          
INFO[0005] Starting Node 'k3d-flyte-server-0'           
ERRO[0009] Failed to start node 'k3d-flyte-server-0'    
ERRO[0009] Failed Cluster Start: Failed to start server k3d-flyte-server-0: Error response from daemon: driver failed programming external connectivity on endpoint k3d-flyte-server-0 (530fbdb32452ab5d66658a5796b274bf7fef7e1471991841f30d42c2edc781bc): Error starting userland proxy: listen tcp4 0.0.0.0:30084: bind: address already in use 
ERRO[0009] Failed to create cluster >>> Rolling Back    
INFO[0009] Deleting cluster 'flyte'                     
INFO[0009] Deleted k3d-flyte-server-0                   
INFO[0009] Deleting cluster network 'k3d-flyte'         
INFO[0012] Deleting image volume 'k3d-flyte-images'     
FATA[0012] Cluster creation FAILED, all changes have been rolled back! 
Rowans-MacBook-Pro:flyte rowan$ k3d cluster create -p "30081:30081" -p "30084:30084" --no-lb --k3s-server-arg '--no-deploy=traefik' --k3s-server-arg '--no-deploy=servicelb' flyte
WARN[0000] No node filter specified                     
WARN[0000] No node filter specified                     
INFO[0000] Prep: Network                                
INFO[0003] Created network 'k3d-flyte' (8e108add3e5198ea83016f016035ae0dad37bb8bf3bcd2f46c771902ee2faa49) 
INFO[0003] Created volume 'k3d-flyte-images'            
INFO[0004] Creating node 'k3d-flyte-server-0'           
INFO[0005] Starting cluster 'flyte'                     
INFO[0005] Starting servers...                          
INFO[0005] Starting Node 'k3d-flyte-server-0'           
INFO[0017] Starting agents...                           
INFO[0017] Starting helpers...                          
INFO[0017] (Optional) Trying to get IP of the docker host and inject it into the cluster as 'host.k3d.internal' for easy access 
INFO[0020] Successfully added host record to /etc/hosts in 1/1 nodes and to the CoreDNS ConfigMap 
INFO[0020] Cluster 'flyte' created successfully!        
INFO[0020] --kubeconfig-update-default=false --> sets --kubeconfig-switch-context=false 
INFO[0021] You can now use it like this:                
kubectl config use-context k3d-flyte
kubectl cluster-info

$ kubectl cluster-info
Kubernetes control plane is running at https://0.0.0.0:49574
CoreDNS is running at https://0.0.0.0:49574/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:49574/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy


Using branch https://github.com/flyteorg/flyte/blob/minimal-perms-jp

$ kubectl apply -f deployment/jpm/flyte_helm_generated.yaml

```

### TOD0 

1) The flyte namespace needs to be changed to 103020-flytepoc-dev
2) Put back the k8s dashboard to support log viewing
3) The limit-namespace config only has the dev domain. The stage & prod domains need to be added as well.

limit-namespace: 10302-flytepoc-flytesnacks-dev-dev

