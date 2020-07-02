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
 ---
## another try
```
kubectl create namespace airflow
helm install airflow stable/airflow --namespace airflow
```
we get:
```
NAME: airflow
LAST DEPLOYED: Mon Jun 15 12:58:40 2020
NAMESPACE: airflow
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Congratulations. You have just deployed Apache Airflow!

1. Get the Airflow Service URL by running these commands:
   export POD_NAME=$(kubectl get pods --namespace airflow -l "component=web,app=airflow" -o jsonpath="{.items[0].metadata.name}")
   echo http://127.0.0.1:8080
   kubectl port-forward --namespace airflow $POD_NAME 8080:8080

2. Open Airflow in your web browser

```


airflow-postgresql-0 存在问题
```
kubectl get pods -n airflow
```
get:
```
NAME                                READY   STATUS    RESTARTS   AGE
airflow-flower-85bb4d6bb4-x9cgg     1/1     Running   0          54s
airflow-postgresql-0                0/1     Pending   0          54s
airflow-redis-master-0              1/1     Running   0          54s
airflow-scheduler-bcc9ff8c8-ksxqv   1/1     Running   0          54s
airflow-web-78d47b8859-tv6fw        1/1     Running   0          54s
airflow-worker-0                    1/1     Running   0          54s
```

Then kubectl describe pod airflow-postgresql-0  -n airflow
```
Name:           airflow-postgresql-0
Namespace:      airflow
Priority:       0
Node:           <none>
Labels:         app=postgresql
                chart=postgresql-8.6.4
                controller-revision-hash=airflow-postgresql-5657f9d89b
                heritage=Helm
                release=airflow
                role=master
                statefulset.kubernetes.io/pod-name=airflow-postgresql-0
Annotations:    <none>
Status:         Pending
IP:             
IPs:            <none>
Controlled By:  StatefulSet/airflow-postgresql
Containers:
  airflow-postgresql:
    Image:      docker.io/bitnami/postgresql:11.7.0-debian-10-r9
    Port:       5432/TCP
    Host Port:  0/TCP
    Requests:
      cpu:      250m
      memory:   256Mi
    Liveness:   exec [/bin/sh -c exec pg_isready -U "postgres" -d "airflow" -h 127.0.0.1 -p 5432] delay=30s timeout=5s period=10s #success=1 #failure=6
    Readiness:  exec [/bin/sh -c -e exec pg_isready -U "postgres" -d "airflow" -h 127.0.0.1 -p 5432
[ -f /opt/bitnami/postgresql/tmp/.initialized ] || [ -f /bitnami/postgresql/.initialized ]
] delay=5s timeout=5s period=10s #success=1 #failure=6
    Environment:
      BITNAMI_DEBUG:           false
      POSTGRESQL_PORT_NUMBER:  5432
      POSTGRESQL_VOLUME_DIR:   /bitnami/postgresql
      PGDATA:                  /bitnami/postgresql/data
      POSTGRES_USER:           postgres
      POSTGRES_PASSWORD:       <set to the key 'postgresql-password' in secret 'airflow-postgresql'>  Optional: false
      POSTGRES_DB:             airflow
      POSTGRESQL_ENABLE_LDAP:  no
    Mounts:
      /bitnami/postgresql from data (rw)
      /dev/shm from dshm (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-l6m2s (ro)
Conditions:
  Type           Status
  PodScheduled   False 
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-airflow-postgresql-0
    ReadOnly:   false
  dshm:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     Memory
    SizeLimit:  1Gi
  default-token-l6m2s:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-l6m2s
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason            Age        From               Message
  ----     ------            ----       ----               -------
  Warning  FailedScheduling  <unknown>  default-scheduler  running "VolumeBinding" filter plugin for pod "airflow-postgresql-0": pod has unbound immediate PersistentVolumeClaims
  Warning  FailedScheduling  <unknown>  default-scheduler  running "VolumeBinding" filter plugin for pod "airflow-postgresql-0": pod has unbound immediate PersistentVolumeClaims

```
```
helm upgrade airflow stable/airflow --namespace airflow --values airflow_values.yaml 
```

---
# another try
[Link](https://blog.csdn.net/wsxx1020/article/details/101519429) failed
[Link](https://www.kutu66.com//GitHub/article_145614) failed

```
conda deactivate
conda create -n k8sAirflow python=3.7
conda activate k8sAirflow
pip install apache-airflow
pip install 'apache-airflow[kubernetes]'
airflow initdb
airflow webserver -p 7400
```