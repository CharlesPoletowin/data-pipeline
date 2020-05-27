# kubeflow
[Link1](https://www.jianshu.com/p/2f7395b0f3fe)
## Kubeflow是什么

Kubeflow 是在 K8S 集群上跑机器学习任务的工具集，提供了 Tensorflow, Pytorch 等等机器/深度学习的计算框架，同时构建容器工作流 Argo 的集成，称为 Pipeline。

1. 对于 官网 的定义:

       Kubeflow is the machine learning toolkit for Kubernetes.

2. 我们可以看出 Kubeflow 是一个基于 kubernetes 的机器学习工具集。 这也就说明了对于 Kubeflow , 我们应该认识到几点：

3. Kubeflow 是基于 Kubernetes 的， 也就是说我们应该在 Kubernetes 上使用 Kubeflow 。

4. 同时 Kubeflow 的基础是 Kubernetes， 其中很多实现的原理都是 Kubernetes， 所以了解 Kubernetes 对于 Kubeflow会有很大的益处。

5. 最为关键的是， Kubeflow 只是个工具集， 本身并不提供任何的功能。 而是通过 Kubeflow部署的各种工具帮助我们进行机器学习。 所以我们应该着眼于 Kubeflow 中的各种工具。

## pipieline 

### pipieline 为什么能成为 Kubeflow 的核心组件？

这就要说到 Kubeflow 这个项目的成立目的了。Kubeflow 主要是为了简化在 Kubernetes 上面运行机器学习任务的流程， 最终希望能够实现一套完整可用的流水线, 来实现机器学习从数据到模型的一整套端到端的过程。 所以从这个层面来说，pipeline 能够成为 Kubeflow 的核心组件一点也不意外。

### pipeline 是什么？

pipeline 是一个工作流平台，能够编译部署机器学习的工作流。对于其功能和目标可以前往 官网。 这次我们就不过多的说明这些了。

### pipeline 是如何工作的呢？

在上图我们可以看到， pipeline最终建立的是一个工作流，工作流的原理就是，每一个组件都定义好自己的输入和输出，然后根据输入和输出关系确定工作流的流程。所以工作流的方式对于组件的复用可以起到很好的作用。

## Katib

对于模型的超参调优，一直都是非常重要的内容。所以 Kubeflow 集成了一个超参调优工具 Katib 。 值得一提的是， Katib并不依赖于特定的机器学习框架。

通过指定超参的类型和范围， Katib 会跑许多个训练任务，并保存它们的结果。

## Istio

如果对 CNCF 的比较感兴趣的话，可以知道 Istio 是一个非常有名的服务网格。 而 Kubeflow主要利用 Istio 来进行资源的管理和进行运维， 例如查看相关资源的指标， 权限的验证， 资源的分配和测试等等。 这样对于我们运行和维护起来会更加方便。

## summary
看完上面这些，我们可以大致了解 Kubeflow 的服务对象其实是希望在 Kubernetes 上部署机器学习任务的科学家或者工程师。所以虽然 Kubeflow 提供了很多有用的工具，但是并不适用于所有的机器学习场景。而且 Kubeflow 主要关注于训练的过程， 对于其他方面，则应该找寻更为合适的框架或者平台。

---

# 详解Kubeflow这一K8S的机器学习利器
[Link2](https://baijiahao.baidu.com/s?id=1664180482170496561&wfr=spider&for=pc)

1. Kubeflow组件

Kubeflow提供了一大堆组件，涵盖了机器学习的方方面面，为了对Kubeflow有个更直观深入的了解，先整体看一下Kubeflow都有哪些组件，并对Kubeflow的主要组件进行简单的介绍：

 + Central Dashboard：Kubeflow的dashboard看板页面
+ Metadata：用于跟踪各数据集、作业与模型
+ Jupyter Notebooks：一个交互式业务IDE编码环境

+ Frameworks for Training：支持的ML框架

  + Chainer
  + MPI
  + MXNet
  + PyTorch
  + TensorFlow
+ Hyperparameter Tuning：Katib，超参数服务器
+ Pipelines：一个ML的工作流组件，用于定义复杂的ML工作流
+ Tools for Serving：提供在上对机器学习模型的部署

  + KFServing
  + Seldon Core Serving
  + TensorFlow Serving(TFJob):提供对Tensorflow模型的在线部署，支持版本控制及无需停止线上服务、切换模型等
  + NVIDIA Triton Inference Server(Triton以前叫TensorRT)
  + TensorFlow Batch Prediction
+ Multi-Tenancy in Kubeflow：Kubeflow中的多租户
+ Fairing：一个将code打包构建image的组件

Kubeflow中大多数组件的实现都是通过定义CRD来工作。目前Kubeflow主要的组件有:
+  Operator是针对不同的机器学习框架提供资源调度和分布式训练的能力(TF-Operator，PyTorch-Operator，Caffe2-Operator，MPI-Operator，MXNet-Operator);
+ Pipelines是一个基于Argo实现了面向机器学习场景的流水线项目，提供机器学习流程的创建、编排调度和管理，还提供了一个Web UI。
+ Katib是基于各个Operator实现的超参数搜索和简单的模型结构搜索的系统，支持并行搜索和分布式训练等。超参优化在实际的工作中还没有被大规模的应用，所以这部分的技术还需要一些时间来成熟;
+ Serving支持部署各个框架训练好的模型的服务化部署和离线预测。Kubeflow提供基于TFServing，KFServing，Seldon等好几种方案。由于机器学习框架很多，算法模型也各种各样。工业界一直缺少一种能真正统一的部署框架和方案。这方面Kubeflow也仅仅是把常见的都集成了进来，但是并没有做更多的抽象和统一。

## summary
以上，我对Kubeflow组件进行了系统的概括，来帮助我们对各个组件有一个基本的了解和整体的把握。趁热打铁，接下来我们详细介绍每一个组件的架构和工作流程。


---
[Link3](https://www.zhihu.com/question/359744585/answer/926629487)
# 如何评价Kubeflow 框架

Kubeflow，最早是从 tf-operator 开始的。tf-operator 原本是开源在 TensorFlow 下的一个子项目，它被用来在 Kubernetes 上更好地支持 TensorFlow 的分布式训练任务。后来随着项目愿景的扩大，谷歌把它独立了出来。

总结来说，Kubeflow 是一个由众多子项目组成的开源产品，它的愿景很简单：在 Kubernetes 上运行机器学习工作负载。目前，Kubeflow 主要的组件有 tf-operator，pytorch-operator，mpi-operator，pipelines，katib，kfserving 等。其中 tf-operator，pytorch-operator，mpi-operator 分别对应着对 TensorFlow，PyTorch 和 Horovod 的分布式训练支持；pipelines 是一个流水线项目，它基于 argo 实现了面向机器学习场景的流水线；katib 是基于各个 operator 实现的超参数搜索和简单的模型结构搜索的系统，支持并行的搜索和分布式的训练等；kfserving 是对模型服务的支持，它支持部署各个框架训练好的模型的在线推理服务。

Kubeflow 想解决的问题是如何基于 Kubernetes 去方便地维护 ML Infra。Kubeflow 中大多数组件的实现都是基于 Kubernetes Native 的解决方案，通过定义 CRD 来功能。这很大程度上减少了运维的工作。同时，利用 Kubernetes 提供的扩展性和调度能力，对大规模分布式训练和 AutoML 也有得天独厚的优势。

从这一角度来说，竞品并没有多少，它可以说是开源领域 ML Infra on Kubernetes 的唯一选择（据我所知）。如果跳出 Kubernetes，从广义的 ML Infra 来说，竞品有 MLFlow，SQLFlow，Microsoft PAI，Ray 等。MLFlow 对用户代码有一定的侵入性，但是如果忽略这一点，它提供的功能更加简洁统一。SQLFlow 面向的场景比较独特，不太具有可比性。Microsoft PAI 跟 kubeflow 比较类似，但是自成一体，并没有选择构建在已有的，如 Kubernetes 等集群调度系统上。Ray 我个人比较看好和欣赏，但目前只对超参数搜索和强化学习支持比较好。

目前就我私下所知，采用了 Kubeflow 的某些 operator 组件的公司有不少。社区中也有非常多活跃的，来自 IBM，Cisco 等等不同公司的贡献者。但因为社区，尤其在国内，一直没有做这方面的 survey，所以没有具体数据，各位同行如果在采用 tf-operator，可以贡献一下
https://github.com/kubeflow/tf-operator/blob/master/docs/adopters.md​
github.com

大部分对 Kubeflow 的采用，应该都是为了在 Kubernetes 上更好地支持机器学习业务，这也是我们公司（才云科技）一直在积极贡献 Kubeflow 的原因。目前，我们在上海正在招聘机器学习平台工程师，会负责公司内部机器学习平台产品的开发，和 Kubeflow 上游的社区贡献工作，各位有兴趣可以私聊我或者发邮件到 gaoce@caicloud.io，鞠躬

利益相关：Kubeflow tf-operator, pytorch-operator, katib 等项目的 owner，工作也与之有关

---

Kubeflow 包含了很多组件，就我组里落地使用的有 tf-operator 和 mpi-operator。对在 Kubernetes 上做机器学习和深度学习的基础设施是非常有用的。

xxx on Kubernetes 应该是17年以来非常热门的架构了，包括我们一直在围绕 Kubernetes 提供各种基础服务，包括存储和计算。机器学习和深度学习方面我们原来模仿 spark-submit 做了个类似 tf-operator 的组件叫做 tf-submit.......，基本上就是根据 TF_CONFIG，来分别配置 PS/Worker 节点的启动参数，训练任务的生命周期是通过 Watch Pod 的状态来做的。但是在容错和重试这些的支持比较弱。那会各种 operator 还没有很多，所以在做分布式的 Tensorflow 训练的时候 tf-submit 基本够用了。

tf-operator 就爽很多了，不同的分布式训练架构，重试，容错，这些都是在实际场景里非常有价值的特性，所以现在我们也切到 tf-operator 去了。