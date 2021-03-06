
# Services

The following services are required in Flyte. The values shown are GKP services that can be used to perform that service.



## Relational Database


The FlyteAdmin and DataCatalog components rely on PostgreSQL to store persistent records.

GAIA CockRoachDB?


## Object store

Core Flyte components such as Admin, Propeller, and DataCatalog, as well as user runtime containers rely on an Object Store to hold files.

Mercury


## Pub/Sub 

Flyte relies on cloud-provided pub/sub and schedulers to provide automated periodic execution of your launch plans.

???


## Authentication

The Flyte system consists of multiple components. Securing communication between each components is crucial to ensure the security of the overall system.

In abstract, Flyte supports OAuth2 and OpenId Connect (built on top of OAuth2) to secure the various connections:

OpenId Connect: Used to secure user’s authentication to flyteadmin service.

OAuth2: Used to secure communication between clients (i.e. flyte-cli, flytectl and flytepropeller) and flyteadmin service.

???

## Monitoring

Flyte Backend is written in Golang and exposes stats using Prometheus. The Stats themselves are labeled with the Workflow, Task, Project & Domain wherever appropriate.

Dash boards
- [user](https://grafana.com/grafana/dashboards/13980)
- [data plane](https://grafana.com/grafana/dashboards/13979)
- [control plane](https://grafana.com/grafana/dashboards/13981)

