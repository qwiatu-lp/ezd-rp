# Installation applications via CLI
----------------------

## Prerequisites

1. Set necessary environments

```bash

export K8S_NAMESPACE=ezd-rp  #desired namespace where ezd-rp will be installed
export K8S_SC=   #set environmental variable for storage class - to get available run: "kubectl get storageclass"

export POSTGRES_HOST=lp-backend-postgresql-rw
export APP_DOMAIN= # set the name of the domain where ezdrp will be exists
export APP_USER_PASSWD=$(openssl rand -hex 10)  # random it by default or set own password
export PSQL_PASSWD=$(openssl rand -hex 10)  # random it by default or set own password
export PSQL_APP_PASSWD=$(openssl rand -hex 10)  # random it by default or set own password
export PSQL_USER=postgres 
export RABBITMQ_PASSWD=$(openssl rand -hex 10)  # random it by default or set own password
export RABBITMQ_USER=ezdrpadmin
export MONGO_PASSWD=$(openssl rand -hex 10)  # random it by default or set own password
export REDIS_PASSWD=$(openssl rand -hex 10)  # random it by default or set own password
```

2. Create and go to the namespace

```bash
kubectl create ns ${K8S_NAMESPACE}
kubectl config set-context --current --namespace ${K8S_NAMESPACE}
```

3. Generate tls secret - the CN field in TLS must contain name `*.<APP_DOMAIN>` where `APP_DOMAIN` is the name set in env `APP_DOMAIN` 


4. Add repositories necessary for installation

```bash 
helm repo add lp-ezd https://linuxpolska.github.io/ezd-rp

helm repo add nask-ezd https://hub.eadministracja.nask.pl/chartrepo/ezdrp 

helm repo update
```


## ezd-crd installation

This chart deploys operators necessary for working ezd-backend. Backend can be installed only once per cluster

1. Install ezd-crd


```bash
helm upgrade --install -n default ezd-crd  lp-ezd/ezd-crd --wait=true

```

## ezd-backend installation

This chart should be installed in the same namespace as ezd-fronted. It deploy mongodb, postgresql, rabbitmq, redis and prepare necessary secrets

1. Preapre file used in installation

```bash

cat <<EOF > /tmp/ezd-backend-db.values
mongodb:
  auth:
    rootPassword: ${MONGO_PASSWD} 
postgresqlConfig:
  auth:
    admPassword:  ${PSQL_PASSWD}
    appPassword:  ${PSQL_APP_PASSWD}
rabbitmqConfig:
  auth:
    password:  ${RABBITMQ_PASSWD}
    username:  ${RABBITMQ_USER}
redisConfig:
  auth:
    password:  ${REDIS_PASSWD}
EOF

```


2. Install ezd-backend

```bash
helm upgrade --install -n ${K8S_NAMESPACE} ezd-backend -f /tmp/ezd-backend-db.values lp-ezd/ezd-backend --wait=true
```

## ezd-frontend installation

This chart deploy ezd-rp forontend developed by NASK.

1. Preapre file used in installation

```bash
cat <<EOF > /tmp/ezdrp-app.values
cloudadmin:
  relationaldb:
    connectionstring:
      archiwum: Host=${POSTGRES_HOST};Port=5432;Database=archiwum;Username=${PSQL_USER};Password=${PSQL_PASSWD}
      ezdrp: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
      ezdrpodczyt: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp_odczyt;Username=${PSQL_USER};Password=${PSQL_PASSWD}
      kuip: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
    connectiontype:
      archiwum: POSTGRESQL
      ezdrp: POSTGRESQL
      ezdrpodczyt: POSTGRESQL
      kuip: POSTGRESQL
domainInfo:
  cert_info: true
  cert_name: ezdrp-cert
  name: ${APP_DOMAIN}
email:
  active: false
ezdrpApi:
  persistence:
    storageClass: ${K8S_SC}
filerepository:
  persistence:
    storageClass: ${K8S_SC}
mongoExt:
  password: ${MONGO_PASSWD}
redisAppendExt:
  isCluster: false
redisExt:
  isCluster: false
ssoIdentityServer:
  persistence:
    storageClass: ${K8S_SC}
useBuiltInMongoDB:
  enabled: true
useBuiltInRabbit:
  enabled: true
useBuiltInRedis:
  enabled: true
useBuiltInRedisAppend:
  enabled: true
wpeRest:
  persistence:
    storageClass: ${K8S_SC}
EOF
```

2. Create secret with generated TLS certificate for application

```bash
kubectl -n ${K8S_NAMESPACE} create secret tls ezdrp-cert --cert=certs/domain.cert.crt --key=certs/domain.cert.key
```

3. Install frontend

```bash
helm upgrade --install ezd-frontend -n ${K8S_NAMESPACE} -f /tmp/ezdrp-app.values nask-ezd/nask-ezdrp-ha --version 1.16.15
#Please remember that version 1.17.11 and above some of pvc requires "ReadWriteMany" acccess whcih will not work with storage class vsphere-csi-rwo, please add --version <ver> to pick different than latest.
```

4. Set up password for ezd-fronted application (GUI)
```bash
kubectl -n ${K8S_NAMESPACE} set env deployment/kuip-api KUIP_ROOT_RESET_PASSWORD=${APP_USER_PASSWD}
```
5. According to summary installation - go to url and login to ezd-rp
