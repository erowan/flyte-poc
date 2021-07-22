# Auth



## Authentication


IDAnywhere needs to be plugged in. Investigate.


## Authorization

Once a user is authenticated, authorization of the control plane (Admin interface) needs to be handled.

Flyte has nothing in place atm just an [RFC](https://docs.google.com/document/d/1-dacHa0iaZl-Nq-nypfjbIqGq3m-Z2rp3T6AmLiburA/edit?usp=sharing)

Looks like the are going to go with [Open Policy Agent (OPA)](https://www.openpolicyagent.org/).




## GKP Roles & Service Accounts

```
$ kubectl get clusterrolebinding | grep flyte
flyte-pod-webhook                                      ClusterRole/flyte-pod-webhook                                                      4d22h
flyteadmin-binding                                     ClusterRole/flyteadmin                                                             4d22h
flytepropeller                                         ClusterRole/flytepropeller                                                         4d22h


$ kubectl describe clusterrolebinding flytepropeller
Name:         flytepropeller
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  flytepropeller
Subjects:
  Kind            Name            Namespace
  ----            ----            ---------
  ServiceAccount  flytepropeller  flyte

$ kubectl describe serviceaccount flytepropeller
Name:                flytepropeller
Namespace:           flyte
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   flytepropeller-token-8hqnq
Tokens:              flytepropeller-token-8hqnq
Events:              <none>

$ kubectl describe clusterrole flytepropeller
Name:         flytepropeller
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources                                       Non-Resource URLs  Resource Names  Verbs
  ---------                                       -----------------  --------------  -----
  events                                          []                 []              [create update delete patch]
  customresourcedefinitions.apiextensions.k8s.io  []                 []              [get list watch create delete update]
  flyteworkflows.flyte.lyft.com/finalizers        []                 []              [get list watch create update delete patch post deletecollection]
  flyteworkflows.flyte.lyft.com                   []                 []              [get list watch create update delete patch post deletecollection]
  *.*                                             []                 []              [get list watch create update delete patch]
  pods                                            []                 []              [get list watch]



$ kubectl describe clusterrolebinding flyteadmin-binding
Name:         flyteadmin-binding
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  flyteadmin
Subjects:
  Kind            Name        Namespace
  ----            ----        ---------
  ServiceAccount  flyteadmin  flyte

$ kubectl describe clusterrole flyteadmin
Name:         flyteadmin
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources                                  Non-Resource URLs  Resource Names  Verbs
  ---------                                  -----------------  --------------  -----
  configmaps                                 []                 []              [*]
  flyteworkflows                             []                 []              [*]
  namespaces                                 []                 []              [*]
  pods                                       []                 []              [*]
  resourcequotas                             []                 []              [*]
  rolebindings                               []                 []              [*]
  roles                                      []                 []              [*]
  secrets                                    []                 []              [*]
  serviceaccounts                            []                 []              [*]
  services                                   []                 []              [*]
  spark-role                                 []                 []              [*]
  configmaps.flyte.lyft.com                  []                 []              [*]
  flyteworkflows.flyte.lyft.com              []                 []              [*]
  namespaces.flyte.lyft.com                  []                 []              [*]
  pods.flyte.lyft.com                        []                 []              [*]
  resourcequotas.flyte.lyft.com              []                 []              [*]
  rolebindings.flyte.lyft.com                []                 []              [*]
  roles.flyte.lyft.com                       []                 []              [*]
  secrets.flyte.lyft.com                     []                 []              [*]
  serviceaccounts.flyte.lyft.com             []                 []              [*]
  services.flyte.lyft.com                    []                 []              [*]
  spark-role.flyte.lyft.com                  []                 []              [*]
  configmaps.rbac.authorization.k8s.io       []                 []              [*]
  flyteworkflows.rbac.authorization.k8s.io   []                 []              [*]
  namespaces.rbac.authorization.k8s.io       []                 []              [*]
  pods.rbac.authorization.k8s.io             []                 []              [*]
  resourcequotas.rbac.authorization.k8s.io   []                 []              [*]
  rolebindings.rbac.authorization.k8s.io     []                 []              [*]
  roles.rbac.authorization.k8s.io            []                 []              [*]
  secrets.rbac.authorization.k8s.io          []                 []              [*]
  serviceaccounts.rbac.authorization.k8s.io  []                 []              [*]
  services.rbac.authorization.k8s.io         []                 []              [*]
  spark-role.rbac.authorization.k8s.io       []                 []              [*]



$ kubectl describe clusterrolebinding flyte-pod-webhook
Name:         flyte-pod-webhook
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  flyte-pod-webhook
Subjects:
  Kind            Name               Namespace
  ----            ----               ---------
  ServiceAccount  flyte-pod-webhook  flyte

Rowans-MacBook-Pro:flyte-poc rowan$ kubectl describe clusterrole flyte-pod-webhook
Name:         flyte-pod-webhook
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources                        Non-Resource URLs  Resource Names  Verbs
  ---------                        -----------------  --------------  -----
  mutatingwebhookconfigurations.*  []                 []              [get create update patch]
  pods.*                           []                 []              [get create update patch]
  secrets.*                        []                 []              [get create update patch]
 ```