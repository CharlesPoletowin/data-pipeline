MLFlow
[Link](https://hub.helm.sh/charts/cetic/mlflow)

According to the official page, it seems similar to airflow which we should use 
[Official Link](https://mlflow.org/docs/latest/quickstart.html)
```
pip3 install mlflow
```

First try something else
```
helm repo add cetic https://cetic.github.io/helm-charts
helm repo update
```
after we get happly helming
```
helm install my-release cetic/mlflow
```
Then we get
```
NAME: my-release
LAST DEPLOYED: Fri Jun 12 10:32:04 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get the application URL by running these commands:
export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services my-release-mlflow)
export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
echo http://$NODE_IP:$NODE_PORT
```
Then we get this
```
http://192.168.201.28:32394
```

Then we find
```
my-release-mlflow-77d7b489b6-wqgsv   0/1     CrashLoopBackOff   6    
```
guess the problem is
```
BACKEND_STORE_URI:       postgresql://admin:password@postgresql.default.svc.cluster.local:5432
```
The whole problem is 
```
Name:         my-release-mlflow-77d7b489b6-z8dmb
Namespace:    default
Priority:     0
Node:         huaxin-02/192.168.201.6
Start Time:   Fri, 12 Jun 2020 11:09:56 +0800
Labels:       app.kubernetes.io/instance=my-release
              app.kubernetes.io/name=mlflow
              pod-template-hash=77d7b489b6
Annotations:  <none>
Status:       Running
IP:           10.44.0.5
IPs:
  IP:           10.44.0.5
Controlled By:  ReplicaSet/my-release-mlflow-77d7b489b6
Containers:
  mlflow:
    Container ID:   docker://d2a9594356df6527ffbb3ade090889aaec6d7392bf1bff29d47c1b648a8b3cb8
    Image:          selltom/mlflow:1.5.0
    Image ID:       docker-pullable://selltom/mlflow@sha256:3c00d7e28d6d09ce2e44a19b28e05a3dadd0252c86009295ad0d1ff59124d637
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Error
      Exit Code:    1
      Started:      Fri, 12 Jun 2020 11:15:44 +0800
      Finished:     Fri, 12 Jun 2020 11:15:45 +0800
    Ready:          False
    Restart Count:  6
    Liveness:       http-get http://:http/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:      http-get http://:http/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:
      BACKEND_STORE_URI:       postgresql://admin:password@postgresql.default.svc.cluster.local:5432
      DEFAULT_ARTIFACT_ROOT:   s3://datalake/mlflow/artifacts
      MLFLOW_S3_ENDPOINT_URL:  minio.default.svc.cluster.local:9000
      AWS_ACCESS_KEY_ID:       secret
      AWS_SECRET_ACCESS_KEY:   secret
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-6vmpv (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-6vmpv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-6vmpv
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                     From                Message
  ----     ------     ----                    ----                -------
  Normal   Scheduled  <unknown>               default-scheduler   Successfully assigned default/my-release-mlflow-77d7b489b6-z8dmb to huaxin-02
  Warning  Unhealthy  9m17s                   kubelet, huaxin-02  Liveness probe failed: Get http://10.44.0.5:80/: dial tcp 10.44.0.5:80: connect: connection refused
  Warning  Unhealthy  9m17s                   kubelet, huaxin-02  Readiness probe failed: Get http://10.44.0.5:80/: dial tcp 10.44.0.5:80: connect: connection refused
  Normal   Started    8m23s (x4 over 9m21s)   kubelet, huaxin-02  Started container mlflow
  Normal   Pulled     7m42s (x5 over 9m21s)   kubelet, huaxin-02  Container image "selltom/mlflow:1.5.0" already present on machine
  Normal   Created    7m42s (x5 over 9m21s)   kubelet, huaxin-02  Created container mlflow
  Warning  BackOff    4m12s (x27 over 9m16s)  kubelet, huaxin-02  Back-off restarting failed container

```
from its official page,
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install postgresql
```
Try to install it
[Link](https://crunchydata.github.io/postgres-operator/stable/installation/postgres-operator/)
```
kubectl create namespace pgo
kubectl apply -f https://raw.githubusercontent.com/CrunchyData/postgres-operator/v4.3.2/installers/kubectl/postgres-operator.yml
```

Then we get 
```
serviceaccount/pgo-deployer-sa created
clusterrole.rbac.authorization.k8s.io/pgo-deployer-cr created
clusterrolebinding.rbac.authorization.k8s.io/pgo-deployer-crb created
job.batch/pgo-deploy created
```
install the pgo Client
```
curl https://raw.githubusercontent.com/CrunchyData/postgres-operator/master/installers/kubectl/client-setup.sh > client-setup.sh
chmod +x client-setup.sh
./client-setup.sh
```
Then we get
```
pgo client files have been generated, please add the following to your bashrc
export PATH=/root/.pgo/pgo:$PATH
export PGOUSER=/root/.pgo/pgo/pgouser
export PGO_CA_CERT=/root/.pgo/pgo/client.crt
export PGO_CLIENT_CERT=/root/.pgo/pgo/client.crt
export PGO_CLIENT_KEY=/root/.pgo/pgo/client.key
```
For convenience, after the script has finished, you can permanently at these environmental variables to your environment:
```
cat <<EOF >> ~/.bashrc
export PATH=/root/.pgo/pgo:$PATH
export PGOUSER=/root/.pgo/pgo/pgouser
export PGO_CA_CERT=/root/.pgo/pgo/client.crt
export PGO_CLIENT_CERT=/root/.pgo/pgo/client.crt
export PGO_CLIENT_KEY=/root/.pgo/pgo/client.key
EOF
```
```
kubectl -n pgo port-forward svc/postgres-operator 8443:8443
```