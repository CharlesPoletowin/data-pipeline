# argo

[Link](https://www.qikqiak.com/post/argo-workflow-engine-for-k8s/)
<br>
[github io link](https://argoproj.github.io/)

## intro
Argo 是 Applatix 推出的一个开源项目，为 Kubernetes 提供 container-native（工作流中的每个步骤是通过容器实现）工作流程。Argo 可以让用户用一个类似于传统的 YAML 文件定义的 DSL 来运行多个步骤的 Pipeline。该框架提供了复杂的循环、条件判断、依赖管理等功能，这有助于提高部署应用程序的灵活性以及配置和依赖的灵活性。使用 Argo，用户可以定义复杂的依赖关系，以编程方式构建复杂的工作流、制品管理，可以将任何步骤的输出结果作为输入链接到后续的步骤中去，并且可以在可视化 UI 界面中监控调度的作业任务。


Argo V2 版本通过 Kubernetes CRD（Custom Resource Definition）来进行实现的，所以我们可以通过 kubectl 工具来管理 Argo 工作流，当然就可以和其他 Kubernetes 资源对象直接集成了，比如 Volumes、Secrets、RBAC 等等。新版本的 Argo 更加轻量级，安装也十分简单，但是依然提供了完整的工作流功能，包括参数替换、制品、Fixture、循环和递归工作流等功能。

Argo 中的工作流自动化是通过使用 ADSL（Argo 领域特定语言）设计的 YAML 模板（因为 Kubernetes 主要也使用相同的 DSL 方式，所以非常容易使用）进行驱动的。ADSL 中提供的每条指令都被看作一段代码，并与代码仓库中的源码一起托管。Argo 支持6中不同的 YAML 结构：

    容器模板：根据需要创建单个容器和参数
    工作流模板：定义一个作业任务（工作流中的一个步骤可以是一个容器）
    策略模板：触发或者调用作业/通知的规则
    部署模板：创建一个长期运行的应用程序模板
    Fixture 模板：整合 Argo 外部的第三方资源
    项目模板：可以在 Argo 目录中访问的工作流定义

Argo 支持几种不同的方式来定义 Kubernetes 资源清单：Ksonnect、Helm Chart 以及简单的 YAML/JSON 资源清单目录。


----

CSDN 
[Link](https://blog.csdn.net/zzq900503/article/details/88037991)

## 特点
1. argo是基于容器的，不需要传统的虚拟机机系统和环境
2. argo是云无关的，可以在任意的k8s集群中运行
3. argo能定制计算资源，让云端的资源在我们的掌握之中

---

## 比较airflow
[Link](https://zhuanlan.zhihu.com/p/62240151)
大家可以关注一下https://argoproj.github.io/ 这个项目，感觉是Airflow的未来最大竞争对手。Airflow对于程序员来说最不爽的一点就是所有调度逻辑都得用python写。而Argo这个项目最大的优势就是workflow调度，逻辑判断都是用自定义的DSL来处理，语言无关的。每一步的执行都是docker image，这就很爽了。

Argo本身除了有workflow功能外，还自带了CI，Events的功能，感觉是要吃掉drone GitLab提供的CI流水线，还有就是要统一Lambda的事件触发。


---
## argo源码入门
[Link](https://blog.csdn.net/oqqYuan1234567890/article/details/104271124)

### 算法工作流

在讨论算法工作流之前，先看一下工作流的介绍。工作流，简单来说是完成一件事的工作流程
像下图所描述的任务，的就是一个典型的工作流

![picture1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9nc3MzLmJkc3RhdGljLmNvbS83UG8zZFNhZ194STRraEdrcG9XSzFIRjZoaHkvYmFpa2UvYzA9YmFpa2U4MCw1LDUsODAsMjYvc2lnbj1mOGEzZmI4OTBhNDZmMjFmZGQzOTU2MDE5NzRkMDAwNS8xOGQ4YmMzZWIxMzUzM2ZhYWRlMjI2NWNhOGQzZmQxZjQxMzQ1YjZmLmpwZw?x-oss-process=image/format,png)

一般来说，工作流是一个有向无环图（DAG）
算法工作流也是一个DAG，里面的一个点，就是一个step，目前我所接触的工作流，有简单到一个step就可以完成的，也有n多个step组成的。每个step，都会有对应的输入，也会有对应的输出，然后构成一个完整的算法pipeline。

一个算法工作流，由一个或者多个算法step构成，但是算法工作流要按照一种预期的方式执行，就需要有配套的调度服务，来保证不同的step有序执行。
算法工作流调度服务，目前社区有两个比较出色的开源项目，airflow以及argo。

### airflow

是apache下孵化的一个工作流引擎，可以裸机部署，也可以部署在k8s集群中

优点:
+ 代码即配置（py实现）
+  提供UI，过程跟踪比较方便

缺点:
+ API开放的功能比较少，做集成比较难度较高
+ 对k8s的支持比较少

### 从两个功能看argo

我目前常用的两个功能，

    DAG or Steps based declaration of workflows
    Artifact support (S3, Artifactory, HTTP, Git, raw)

DAG or Steps based declaration of workflows

github.com/argoproj/argo/examples/dag-diamond-steps.yaml

```
# The following workflow executes a diamond workflow
# 
#   A
#  / \
# B   C
#  \ /
#   D
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: dag-diamond-
spec:
  entrypoint: diamond
  templates:
  - name: diamond
    dag:
      tasks:
      - name: A
        template: echo
        arguments:
          parameters: [{name: message, value: A}]
      - name: B
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: B}]
      - name: C
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: C}]
      - name: D
        dependencies: [B, C]
        template: echo
        arguments:
          parameters: [{name: message, value: D}]

  - name: echo
    inputs:
      parameters:
      - name: message
    container:
      image: alpine:3.7
      command: [echo, "{{inputs.parameters.message}}"]
```

这是一个钻石形状的DAG工作流
第一阶段：执行A
第二阶段：B和C是同时执行的
第三阶段：D需要等B和C同时成功执行才会触发（注意：argo目前支持的是条件与，暂时不支持条件或，假如D的触发条件是B或C任意一个完成，这种情况需要定制开发）

