# airflow

```
pip3 install apache-airflow
```
 - 发现由于python版本问题无法这样安装，需要至少3.6以上的版本

[Link](https://docs.bitnami.com/tutorials/deploy-apache-airflow-kubernetes-helm/)
后续按照这篇尝试安装
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
```
helm install myrelease bitnami/airflow --set airflow.cloneDagFilesFromGit.enabled=true --set airflow.cloneDagFilesFromGit.repository=REPOSITORY_URL   --set airflow.cloneDagFilesFromGit.branch=master --set airflow.baseUrl=http://127.0.0.1:8080 
```

Then we get
```
NAME: myrelease
LAST DEPLOYED: Tue Jun  9 19:26:25 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get the Airflow URL by running:

  echo URL  : http://127.0.0.1:8080
  kubectl port-forward --namespace default svc/myrelease-airflow 8080:8080

2. Get your Airflow login credentials by running:

  echo User:    user
  echo Password: $(kubectl get secret --namespace default myrelease-airflow -o jsonpath="{.data.airflow-password}" | base64 --decode)
```
同样存在问题无法成功

```
helm install airflow stable/airflow \
  --namespace "airflow" 
```

try again
```
helm install my-release bitnami/postgresql
```
get
```
NAME: my-release
LAST DEPLOYED: Tue Jun  9 20:57:21 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

PostgreSQL can be accessed via port 5432 on the following DNS name from within your cluster:

    my-release-postgresql.default.svc.cluster.local - Read/Write connection

To get the password for "postgres" run:

    export POSTGRES_PASSWORD=$(kubectl get secret --namespace default my-release-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)

To connect to your database run the following command:

    kubectl run my-release-postgresql-client --rm --tty -i --restart='Never' --namespace default --image docker.io/bitnami/postgresql:11.8.0-debian-10-r19 --env="PGPASSWORD=$POSTGRES_PASSWORD" --command -- psql --host my-release-postgresql -U postgres -d postgres -p 5432



To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace default svc/my-release-postgresql 5432:5432 &
    PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d postgres -p 5432
```
install 
```
helm install my-release bitnami/airflow
```
get
```
NAME: my-release2
LAST DEPLOYED: Tue Jun  9 21:00:08 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
###############################################################################
### ERROR: You did not provide an external URL in your 'helm install' call ###
###############################################################################

This deployment will be incomplete until you configure Airflow with a resolvable
host. To configure Airflow with the URL of your service:
1. Get the Airflow URL by running:

  export APP_HOST=127.0.0.1
  export APP_PORT=8080
  export APP_PASSWORD=$(kubectl get secret --namespace default my-release2-airflow -o jsonpath="{.data.airflow-password}" | base64 --decode)
  export APP_DATABASE_PASSWORD=$(kubectl get secret --namespace default my-release2-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)
  export APP_REDIS_PASSWORD=$(kubectl get secret --namespace default my-release2-redis -o jsonpath="{.data.redis-password}" | base64 --decode)
2. Complete your Airflow deployment by running:

  helm upgrade my-release2 bitnami/airflow \
    --set service.type=ClusterIP,airflow.baseUrl=http://$APP_HOST:$APP_PORT,airflow.auth.password=$APP_PASSWORD,postgresql.postgresqlPassword=$APP_DATABASE_PASSWORD,redis.password=$APP_REDIS_PASSWORD

```

then after upgrade, we get
```
NAME: my-release2
LAST DEPLOYED: Tue Jun  9 21:08:11 2020
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
1. Get the Airflow URL by running:

  echo URL  : http://127.0.0.1:8080
  kubectl port-forward --namespace default svc/my-release2-airflow 8080:8080

2. Get your Airflow login credentials by running:

  echo User:    user
  echo Password: $(kubectl get secret --namespace default my-release2-airflow -o jsonpath="{.data.airflow-password}" | base64 --decode)

```