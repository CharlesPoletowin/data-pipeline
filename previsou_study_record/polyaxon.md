# Polyaxon
## 我们用Polyaxon来干嘛

我们可以通过Polyaxon上传本地代码到集群，然后通过写polyaxon配置文件，定制我们所需要的docker镜像，所需要的资源(内存，CPU，GPU)，以及实验类型(jupyter notebook, tensorboard 或者 普通实验)等选项来创建一个或者一组实验(运行在集群指定节点的一个个容器里面)，同时通过web界面的dashboard或者polyaxon-cli提供的众多命令监控资源和状态，跟踪参数，日志，配置和标签，以及最终的结果和输出等。
1. a platform for building, training, and monitoring large scale deep learning applications.
2. install 
https://github.com/polyaxon/polyaxon 

 * Create a deployment
```
# Create a namespace
$ kubectl create namespace polyaxon

# Add Polyaxon charts repo
$ helm repo add polyaxon https://charts.polyaxon.com

# Deploy Polyaxon
$ helm install polyaxon/polyaxon \
    --name=polyaxon \
    --namespace=polyaxon \
    -f config.yaml
```
* Install CLI
```
# Install Polyaxon CLI
$ pip install -U polyaxon-cli

# Config Polyaxon CLI
$ polyaxon config ...

# Login to your account
$ polyaxon login
```

3. Dashboard 
```
polyaxon dashboard
```

4. 文档
[official docs](https://docs.polyaxon.com/guides/ "guides") 

---


# 使用kubeadm建立kubernetes集群并部署Polyaxon详细教程

[Link](https://blog.csdn.net/yuguo7336761/article/details/81150152)

首先简单总结一下，就是先安装 Docker，再安装 Kubernetes，最后安装 Polyaxon。Kubernetes 的安装方式非常多，详见官方教程。这里我准备使用比较简单的方法，就是使用 Kubeadm 来自动安装 Kubenetes，但是这个方式与后续的 polyaxon 会有一定的冲突需要解决，请多加注意。


# install tutorial
[Link](https://blog.csdn.net/yuguo7336761/article/details/81150152)
PersistentVolume（PV） 是群集中由管理员配置的一块存储。它是集群中的资源，就像节点是集群资源一样。PV 是存储卷插件，基本特性类似存储卷 （Volumes），但具有独立于使用 PV 的任何 pod 的生命周期，就是可以提供持久性存储，数据不会随着 pod 结束而消失。此 API 对象捕获存储实现的详细信息，包括 NFS，iSCSI 或特定于云提供程序的存储系统。

PersistentVolumeClaim（PVC） 是用户存储的请求。它类似于一个 pod。Pod消耗节点 （node） 资源，PVC消耗PV资源。Pod可以请求特定级别的资源 （CPU 和内存）。Pvc 可以请求特定的存储大小和访问模式（例如，可以挂载为一次读/写或多次只读）。

我的理解就是 pvc 对用户请求储存空间进行了一层封装，pv 对于系统提供的存储进行了一层封装，这样就可以很好的解耦。Kubernetes 支持的 pv 实现方式有很多，比如 NFS、iSCSI、AzureFile、CephFS等，我们使用比较常见的 NFS 方式来提供存储空间。

# Ubuntu 16.04系统上NFS的安装与使用
[Link](https://blog.csdn.net/CSDN_duomaomao/article/details/77822883)