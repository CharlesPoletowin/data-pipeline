# seldon
[官方](https://www.seldon.io/tech/products/core/)

## intro
 1. Open-source platform for rapidly deploying machine learning models on Kubernetes
 2. Seldon Core, our open-source framework, makes it easier and faster to deploy your machine learning models and experiments at scale on Kubernetes.
 3. Seldon Core serves models built in any open-source or commercial model building framework. You can make use of powerful Kubernetes features like custom resource definitions to manage model graphs. And then connect your continuous integration and deployment (CI/CD) tools to scale and update your deployment.

 ## documentation 
 [文档](https://docs.seldon.io/projects/seldon-core/en/latest/)

 ### overview
 Seldon core converts your ML models (Tensorflow, Pytorch, H2o, etc.) or language wrappers (Python, Java, etc.) into production REST/GRPC microservices.  
<br>
### install
```
kubectl create namespace seldon-system
helm install seldon-core seldon-core-operator \
    --repo https://storage.googleapis.com/seldon-charts \
    --set usageMetrics.enabled=true \
    --namespace seldon-system \
    --set istio.enabled=true
    # You can set ambassador instead with --set ambassador.enabled=true
```
