# Flyte POC on GKP

The Flyte POC aims to get Flyte running on GKP with a limited setup.

- Local postgres db
- No authentication/authorisation
- Mercury auth bootstrapped in the propeller pod and mercury keys passed via a secret using a webhook to the project workflow pods? I think the S3 bucket has global scope within a single Flyte deployment currently. This shold be changed to be scoped at the project level to isolate data between different projects. Note the Mecury service does not currently support scoping buckets within a seal app id either yet.  
- Limited admin functionality?
  The Admin control plane requires cluster roles to perform its actions (create namespaces etc). Flyte are investigating if the cluster roles can be reduced to namespace roles but we may have to create the required project resources manually until this is resolved.
- No kubernetes-dashboard

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

<appid>-<project>-<env>

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

Flyte has both kustomize & helm3 charts which are both deployment options for us. Find out which system is going to become the defacto at Flyte and base the decision on that to avoid our depoloyment code base gettting out of date with the opensource. Merging changes from helm to kustomize or kustomize to helm would be tiresome.