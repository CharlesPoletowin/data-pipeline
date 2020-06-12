## change hosts
```
vi /etc/hosts/
```
insert
```
192.168.201.28  huaxin-01
192.168.201.6   huaxin-02
192.168.201.26  huaxin-03
```
## install docker
```
apt install docker.io -y
```

## add kubernetes aliyun
```
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
vim /etc/apt/sources.list.d/kubernetes.list
```

```
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
```

## 关闭swap
```
swapoff -a
vi /etc/fstab
```
 + 用vi修改/etc/fstab文件，在swap分区这行前加 # 禁用掉，保存退出

## 安装kubeadm/kubelet/kubectl
```
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

## 配置docker mirror
创建（或修改）/etc/docker/daemon.json
```
vim /etc/docker/daemon.json
```
```
{
"registry-mirrors": ["https://registry.docker-cn.com"]
}
```
## restart docker
```
systemctl restart docker
```
# Master安装

由于Kubernetes运行过程中需要从google的k8s.gcr.io仓库下载基础镜像，但我们无法访问国外仓库，在构建过程下载不大现实，因此我们可以先把docker镜像pull下来，用别名的方式改成对应版本所需要的镜像ID，这样kubeadm init过程需要的镜像就满足要求了。
<br/>
首先我们需要确认要安装的Kubernetes版本对应依赖哪些镜像：

```
kubeadm config images list --kubernetes-version v1.18.0
```
what we get:
```
k8s.gcr.io/kube-apiserver:v1.18.0
k8s.gcr.io/kube-controller-manager:v1.18.0
k8s.gcr.io/kube-scheduler:v1.18.0
k8s.gcr.io/kube-proxy:v1.18.0
k8s.gcr.io/pause:3.2
k8s.gcr.io/etcd:3.4.3-0
k8s.gcr.io/coredns:1.6.7
```
Then we insert
```
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.18.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.18.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.18.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.18.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.4.3-0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7
```
为镜像打tag做别名
```
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.18.0 k8s.gcr.io/kube-controller-manager:v1.18.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.18.0 k8s.gcr.io/kube-apiserver:v1.18.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.18.0 k8s.gcr.io/kube-scheduler:v1.18.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.18.0 k8s.gcr.io/kube-proxy:v1.18.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2 k8s.gcr.io/pause:3.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.4.3-0 k8s.gcr.io/etcd:3.4.3-0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7 k8s.gcr.io/coredns:1.6.7
```
保留别名镜像，移除下载镜像（非真正删除）
```
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.18.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.18.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.18.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.18.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.4.3-0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7
```

## start insatll
```
kubeadm init --kubernetes-version v1.18.0
```

## save important lines
```
kubeadm join 192.168.201.28:6443 --token pfsqr7.m6smx9vz8hqihd99 --discovery-token-ca-cert-hash sha256:11a9248cc2d94b6e9ca7cec776091c8d31f7038309fede9837e2ba7c9793d6ec
```


To start using your cluster, you need to run the following as a regular user:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

## after 1 miniute
```
kubectl get pods -n kube-system
```
then we get
```
NAME                                READY   STATUS    RESTARTS   AGE
coredns-66bff467f8-hpdvq            0/1     Pending   0          17m
coredns-66bff467f8-ttjz5            0/1     Pending   0          17m
etcd-huaxin-01                      1/1     Running   0          17m
kube-apiserver-huaxin-01            1/1     Running   0          17m
kube-controller-manager-huaxin-01   1/1     Running   0          17m
kube-proxy-bpb9p                    1/1     Running   0          17m
kube-scheduler-huaxin-01            1/1     Running   0          17m
```
## Network
```
# 开启透明网桥
sysctl net.bridge.bridge-nf-call-iptables=1 -w
# 应用对应kubernetes版本的weave配置文件
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
Now, insert
```
kubectl get pods -n kube-system
```
we will see, coredns is ready.
```
NAME                                READY   STATUS    RESTARTS   AGE
coredns-66bff467f8-hpdvq            1/1     Running   0          27m
coredns-66bff467f8-ttjz5            1/1     Running   0          27m
etcd-huaxin-01                      1/1     Running   0          27m
kube-apiserver-huaxin-01            1/1     Running   0          27m
kube-controller-manager-huaxin-01   1/1     Running   0          27m
kube-proxy-bpb9p                    1/1     Running   0          27m
kube-scheduler-huaxin-01            1/1     Running   0          27m
weave-net-pqb9g                     2/2     Running   0          84s
```
if we insert 
```
kubectl get nodes
```
then we will get
```
NAME        STATUS   ROLES    AGE   VERSION
huaxin-01   Ready    master   30m   v1.18.2
```
# node install
## 拉取镜像
```
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.18.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.18.0 k8s.gcr.io/kube-proxy:v1.18.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.18.0
```
```
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2 k8s.gcr.io/pause:3.2
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
```
拉取完毕以后，我们把刚才安装master时候输出的命令在node上执行，让其加入master作为node节点：
## join master
```
kubeadm join 192.168.201.28:6443 --token pfsqr7.m6smx9vz8hqihd99 --discovery-token-ca-cert-hash sha256:11a9248cc2d94b6e9ca7cec776091c8d31f7038309fede9837e2ba7c9793d6ec
```
wait for several miniutes
run in master
```
kubectl get nodes
```
and
```
kubectl get pods -n kube-system
```
to check whether we have some errors.

## 至此我们已经完成k8s的基本安装

---

# helm install

[official Link](https://helm.sh/docs/intro/install/)
[github release](https://github.com/helm/helm/releases)
```
wget https://get.helm.sh/helm-v3.2.1-linux-amd64.tar.gz
tar -zxvf helm-v3.2.1-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
helm help
```
# Polyaxon install
```
kubectl create namespace polyaxon
helm repo add polyaxon https://charts.polyaxon.com
helm repo update
```

# install NFS
## NFS kernel
in 192.168.201.6 install nfs-kernel-server
```
apt install nfs-kernel-server
vim /etc/exports
```
编写配置文件,add following lines
```
/home/inesa/nfs/code *(rw,sync,no_subtree_check,no_root_squash)
/home/inesa/nfs/data *(rw,sync,no_subtree_check,no_root_squash)
/home/inesa/nfs/logs *(rw,sync,no_subtree_check,no_root_squash)
/home/inesa/nfs/outputs *(rw,sync,no_subtree_check,no_root_squash)
/home/inesa/nfs/repos *(rw,sync,no_subtree_check,no_root_squash)
/home/inesa/nfs/upload *(rw,sync,no_subtree_check,no_root_squash)
```

然后在 NFS 服务器上面创建这些共享目录
```
mkdir -p /home/inesa/nfs/code  -m 777
mkdir -p /home/inesa/nfs/data -m 777
mkdir -p /home/inesa/nfs/logs -m 777
mkdir -p /home/inesa/nfs/outputs -m 777
mkdir -p /home/inesa/nfs/repos -m 777
mkdir -p /home/inesa/nfs/upload -m 777
```

restart nfs
```
service nfs-kernel-server restart
```
## NFS common
in 10.200.43.8
```
apt install nfs-common
```
连接到 NFS 服务器，在 Master 机器上检查一下是否可以正常连接到 NFS 服务器 
```
showmount -e 192.168.201.6
```
what we get here:
```
Export list for 192.168.201.6:
/home/inesa/nfs/upload  *
/home/inesa/nfs/repos   *
/home/inesa/nfs/outputs *
/home/inesa/nfs/logs    *
/home/inesa/nfs/data    *
/home/inesa/nfs/code    *
```

```
kubectl create -f pvc.yml -n polyaxon
```

```
helm install polyaxon polyaxon/polyaxon --namespace=polyaxon -f polyaxon-config.yml
```

if luckily, we will get 
```
NAME: polyaxon
LAST DEPLOYED: Thu May 28 10:15:17 2020
NAMESPACE: polyaxon
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Polyaxon is currently running:


1. Get the application URL by running these commands:

     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status by running:
           'kubectl get --namespace polyaxon svc -w polyaxon-polyaxon-api'


  export POLYAXON_IP=$(kubectl get svc --namespace polyaxon polyaxon-polyaxon-api -o jsonpath='{.spec.clusterIP}')

  export POLYAXON_PORT=80

  echo http://$POLYAXON_IP:$POLYAXON_PORT

2. Setup your cli by running theses commands:
  polyaxon config set --host=$POLYAXON_IP --port=$POLYAXON_PORT

3. Log in with superuser

  USER: root
  PASSWORD: Get login password with

    kubectl get secret --namespace polyaxon polyaxon-polyaxon-secret -o jsonpath="{.data.POLYAXON_ADMIN_PASSWORD}" | base64 --decode
init user is root and key is rootpassword
```

actually I first use the commands without config file, then I only use part of the config file, finally I get 
<br/>
The commands I use 
```
helm upgrade polyaxon polyaxon/polyaxon --namespace=polyaxon -f p2.yml 
```

```
Release "polyaxon" has been upgraded. Happy Helming!
NAME: polyaxon
LAST DEPLOYED: Tue Jun  2 11:55:01 2020
NAMESPACE: polyaxon
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
Polyaxon is currently running:


1. Get the application URL by running these commands:

     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status by running:
           'kubectl get --namespace polyaxon svc -w polyaxon-polyaxon-api'


  export POLYAXON_IP=$(kubectl get svc --namespace polyaxon polyaxon-polyaxon-api -o jsonpath='{.spec.clusterIP}')

  export POLYAXON_PORT=80

  echo http://$POLYAXON_IP:$POLYAXON_PORT

2. Setup your cli by running theses commands:
  polyaxon config set --host=$POLYAXON_IP --port=$POLYAXON_PORT

3. Log in with superuser

  USER: root
  PASSWORD: Get login password with

    kubectl get secret --namespace polyaxon polyaxon-polyaxon-secret -o jsonpath="{.data.POLYAXON_ADMIN_PASSWORD}" | base64 --decode

```

# install ksonnet
```
wget https://github.com/ksonnet/ksonnet/releases/download/v0.13.1/ks_0.13.1_linux_amd64.tar.gz
tar -zxvf ks_0.13.1_linux_amd64.tar.gz 
mv ks_0.13.1_linux_amd64/ks /usr/local/bin/
ks
```

# install argo
## 1. Download the Argo CLI
```
# Download the binary
curl -sLO https://github.com/argoproj/argo/releases/download/v2.8.1/argo-linux-amd64

# Make binary executable
chmod +x argo-linux-amd64

# Move binary to path
mv ./argo-linux-amd64 /usr/local/bin/argo

# Test installation
argo version
```

## 2. Install the Controller
```
kubectl create namespace argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/install.yaml
```
## Granting admin privileges
```
kubectl create rolebinding default-admin --clusterrole=admin --serviceaccount=default:default
```

## run sample
```
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/coinflip.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/loops-maps.yaml
```
```
argo list
argo get xxx-workflow-name-xxx
argo logs xxx-pod-name-xxx #from get command above
```

## Argo UI
```
kubectl -n argo port-forward deployment/argo-server 2746:2746
```
or just run
```
argo server
```

# install kfctl

```
wget https://github.com/kubeflow/kubeflow/releases/download/v1.0/kfctl_v1.0-0-g94c35cf_linux.tar.gz
tar -zxvf kfctl_v1.0-0-g94c35cf_linux.tar.gz
mv kfctl /usr/local/bin/
```

