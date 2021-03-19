## minikube Installation

### prerequisite

```
brew isntall kubectl
```

安装 virtual box： https://virtualbox.org

### install minikube

```
brew install minikube
```


## Start minikube

```
minikube start --vm-driver=virtualbox --image-repository registry.aliyuncs.com/google_containers
```

注：k8s.gcr.io默认墙，使用--image-repository 参数指回墙内。

start 子命令运行过程，依次下载依赖：
```
kubelet
kubeadm
kubectl
```

## Create deployment 

* 1. Create a deopoyment using a existing image named echoserver
```
 kubectl create deployment hello-minikube --image=registry.aliyuncs.com/google_containers/echoserver:1.10
```

* 2. To access the hello-minikube Deployment, expose it as Service:

```
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```