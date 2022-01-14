# Kubernetes实战


参考[杜先生的博客](https://www.cnblogs.com/dukuan/category/1320955.html)
参考[官网](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)
参考[k8s Deployment](https://blog.csdn.net/xts_huangxin/article/details/51891709)


## 创建集群
    Kubernetes 集群由两种类型的资源组成:
    1. 一个 Master 是集群的调度节点
    2. Nodes 是应用程序实际运行的工作节点

    Master 负责管理集群。 master 协调集群中的所有活动，例如调度应用程序、维护应用程序的所需状态、扩展应用程序和滚动更新。
    节点 是 Kubernetes 集群中的工作机器，可以是物理机或虚拟机。 每个工作节点都有一个 Kubelet，它是管理 节点 并与 Kubernetes Master 节点进行通信的代理。节点 上还应具有处理容器操作的工作，例如 Docker 或 rkt。一个 Kubernetes 工作集群至少有三个节点。

    当您在 Kubernetes 上部署应用程序时，您可以告诉 master 启动应用程序容器。Master 调度容器在集群的 节点 上运行。 节点 使用 Master 公开的 Kubernetes API 与 Master 通信。最终用户还可以直接使用 Kubernetes 的 API 与集群交互。


### 使用Minikube
    创建Kubernetes cluster（单机版）最简单的方法

    1. 安装kubectl(Kubernetes 自带的命令行客户端，可以用它来直接操作 Kubernetes)[UBUNTU]

```bash
wget http://029145.oss-cn-hangzhou.aliyuncs.com/kubectl
sudo chmod u+x kubectl
sudo mv kubectl /usr/local/bin/
```

    2. 安装minikube

```bash
wget http://029145.oss-cn-hangzhou.aliyuncs.com/minikube-linux-amd64
mv minikube-linux-amd64 minikube
chmod u+x minikube
sudo mv minikube /usr/local/bin/
```

    3. 下载依赖的镜像
        注意： 此处的命令都必须在minikube ssh中执行。

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0
docker tag registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0 gcr.io/google_containers/pause-amd64:3.0

docker pull registry.cn-hangzhou.aliyuncs.com/google-containers/kube-addon-manager-amd64:v6.1
docker tag registry.cn-hangzhou.aliyuncs.com/google-containers/kube-addon-manager-amd64:v6.1 gcr.io/google-containers/kube-addon-manager:v6.1

docker pull registry.cn-hangzhou.aliyuncs.com/google-containers/kubedns-amd64:1.9
docker tag registry.cn-hangzhou.aliyuncs.com/google-containers/kubedns-amd64:1.9 gcr.io/google_containers/kubedns-amd64:1.9

docker pull registry.cn-hangzhou.aliyuncs.com/google-containers/kube-dnsmasq-amd64:1.4
docker tag registry.cn-hangzhou.aliyuncs.com/google-containers/kube-dnsmasq-amd64:1.4 gcr.io/google_containers/kube-dnsmasq-amd64:1.4

docker pull registry.cn-hangzhou.aliyuncs.com/google-containers/exechealthz-amd64:1.2
docker tag registry.cn-hangzhou.aliyuncs.com/google-containers/exechealthz-amd64:1.2 gcr.io/google_containers/exechealthz-amd64:1.2

docker pull registry.cn-hangzhou.aliyuncs.com/google-containers/kubernetes-dashboard-amd64:v1.5.0
docker tag registry.cn-hangzhou.aliyuncs.com/google-containers/kubernetes-dashboard-amd64:v1.5.0 gcr.io/google_containers/kubernetes-dashboard-amd64:v1.5.1
```

    4. 启动minikube

```bash
minikube start     "启动"
minikube ssh       "进入minikube终端"
minikube dashboard "在浏览器中打开dashboard"
```

## 部署应用

### 创建一个Kubernetes  Deployment
    Deployment负责创建和更新应用。创建Deployment后，Kubernetes master 会将Deployment创建好的应用实例调度到集群中的各个节点。    
    创建应用程序实例后，Kubernetes 部署控制器会持续监视这些实例。如果托管它的节点不可用或删除，则部署控制器将替换实例。 这提供了一种解决机器故障或维护的自愈机制。

```python
kubectl cluster-info    "查看集群信息"
kubectl get nodes       "查看集群中的节点"
kubectl describe nodes [node-name]    "查看node的详细信息"
kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080    "创建一个deployment"
kubectl get deployments "列出deployments列表：DESIRED为期望的Pod数量，CURRENT为当前的数量，UP-TO-DATE为已更新的数量，AVAILABLE为已运行的数量"
kubectl describe deployments [deployment-name]    "查看deployment详细信息"
kubectl get pods        "查看pods状态信息"
kubectl describe pod [pod-name]    "查看pod详细信息"
kubectl delete pods [pod-name]     "删除pod,会发现又新建了一个"
kubectl logs [pod-name]            "查看pod中运行的容器的日志，一个pod中只运行一个容器"
kubectl exec $POD_NAME env         "在pod中执行命令"
kubectl exec -ti $POD_NAME bash    "打开pod中shell终端"
```

    Pods运行在集群内部，运行在一个私有的隔离的网络中，内部pods和services可以访问，在网络外不可以访问。kubectl命令可以创建一个代理，将通信转发到集群范围内的私有网络。

```
在一个新的终端窗口
kubectl proxy     "创建一个APIserver供主机访问"
```
    
    API服务器将根据pod名称为每个pod自动创建端点，这些端点也可以通过代理访问。

```
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')    "获取pod名称存在环境变量中"
curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-bootcamp-4054965725-sz759/proxy/     "可以访问pod中的应用"
```  

#### 使用yaml文件创建Deployment（并将此文件加入版本控制）
    1. 这是k8s官方推荐的做法 官方例子(https://github.com/kubernetes/examples/blob/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
    2. 将相关的项目放在一个文件中；
    3. 不要在不必要的情况下，指定默认值；
    4. 指定labels（除了在生产环境中）。

```
kubectl create -f deployment.yaml -record         "创建"
kubectl apply -f update-nginx-deployment.yaml     "升级"
ubectl get deployment [deployment-name] -o yaml   "根据现有的deployment生成yml"
```

```yaml
kind: Deployment
apiVersion:	extensions/v1beta1
metadata:
	name: kubernetes-bootcamp
	# namespace: default
	# selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/kubernetes-bootcamp  # 不必要
    # uid:	21ee3d67-0750-11e9-9590-08002716e6be
	# resourceVersion:	142715
	# generation:	2
	# creationTimestamp:	2018-12-24T07:47:15Z
	# labels
		# run:	kubernetes-bootcamp
	# annotations
		# deployment.kubernetes.io/revision	:	1
spec:
    replicas: 4
    # minReadySeconds: 60     #滚动升级时60s后认为该pod就绪
	# selector
		# matchLabels
		    # run	:	kubernetes-bootcamp
	template:
		metadata:
		    #creationTimestamp:	null
            labels:
                [可以指定标签]
		        #run: kubernetes-bootcamp
        spec:
            imagePullSecrets:
            - name: # secret名称
		    containers:
		    -   name:	kubernetes-bootcamp
		        image:	registry.cn-hangzhou.aliyuncs.com/js_k8s/kubernetes-bootcamp:v1
		        ports:
		        -   containerPort	:	8080
                    protocol	:	TCP
                env:
                -   name: LOGDNA_AGENT_KEY
                    valueFrom:
                        secretKeyRef:
                            name: logdna-agent-key
                            key: logdna-agent-key
                -   name: LOGDNA_PLATFORM
                    value: k8s
                -   name: LOGDNA_EXCLUDE
                    value: /var/log/containers/calico-*,/var/log/containers/ibm-kube-fluentd-* 
		        # resources:
		        #terminationMessagePath: /dev/termination-log
                # imagePullPolicy: IfNotPresent
		    # restartPolicy	:	Always
		    # terminationGracePeriodSeconds: 30    #k8s将会给应用发送SIGTERM信号，可以用来正确、优雅地关闭应用,默认为30秒
		    # dnsPolicy	:	ClusterFirst
		    #securityContext:
    # strategy:
	    # type:	RollingUpdate
	    # rollingUpdate:  # 滚动升级配置
	        # maxUnavailable: 1    # 滚动升级时会先启动1个pod
		    # maxSurge:	1    # 滚动升级时允许的最大Unavailable的pod个数
# status:
	# observedGeneration	:	2
	# replicas	:	4
	# updatedReplicas	:	4
	# availableReplicas	:	4
	# conditions:
		# type	:	Available
		# status	:	True
		# lastUpdateTime	:	2018-12-25T06:18:35Z
		# lastTransitionTime	:	2018-12-25T06:18:35Z
		# reason	:	MinimumReplicasAvailable
		# message	:	Deployment has minimum availability.
```

#### replication controller（RC）与deployment的区别

摘自[K8S中RC与Deployment的区别](https://www.cnblogs.com/boshen-hzb/p/7097811.html)

Replication Controller为Kubernetes的一个核心内容，应用托管到Kubernetes之后，需要保证应用能够持续的运行，Replication Controller就是这个保证的key，主要的功能如下：
    确保pod数量：它会确保Kubernetes中有指定数量的Pod在运行。如果少于指定数量的pod，Replication Controller会创建新的，反之则会删除掉多余的以保证Pod数量不变。
    确保pod健康：当pod不健康，运行出错或者无法提供服务时，Replication Controller也会杀死不健康的pod，重新创建新的。
    弹性伸缩 ：在业务高峰或者低峰期的时候，可以通过Replication Controller动态的调整pod的数量来提高资源的利用率。同时，配置相应的监控功能（Hroizontal Pod Autoscaler），会定时自动从监控平台获取Replication Controller关联pod的整体资源使用情况，做到自动伸缩。
    滚动升级：滚动升级为一种平滑的升级方式，通过逐步替换的策略，保证整体系统的稳定，在初始化升级的时候就可以及时发现和解决问题，避免问题不断扩大。

Deployment同样为Kubernetes的一个核心内容，主要职责同样是为了保证pod的数量和健康，90%的功能与Replication Controller完全一样，可以看做新一代的Replication Controller。但是，它又具备了Replication Controller之外的新特性：
    Replication Controller全部功能：Deployment继承了上面描述的Replication Controller全部功能。
    事件和状态查看：可以查看Deployment的升级详细进度和状态。
    回滚：当升级pod镜像或者相关参数的时候发现问题，可以使用回滚操作回滚到上一个稳定的版本或者指定的版本。
    版本记录: 每一次对Deployment的操作，都能保存下来，给予后续可能的回滚使用。
    暂停和启动：对于每一次升级，都能够随时暂停和启动。
    多种升级方案：Recreate：删除所有已存在的pod,重新创建新的; RollingUpdate：滚动升级，逐步替换的策略，同时滚动升级时，支持更多的附加参数，例如设置最大不可用pod数量，最小升级间隔时间等等。

#### 删除Deployment

kubectl delete指令可以用来删除Deployment

#### 其他

Replica Set（简称RS）是k8s新一代的Pod controller。与RC相比仅有selector存在差异，RS支持了set-based selector（可以使用in、notin、key存在、key不存在四种方式来选择满足条件的label集合）。Deployment是基于RS实现的。

```
kubectl get rs
```


### 应用外部可见(变成一个service)

```python
kubectl get services            "查看服务列表"
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080       "使一个deployment外部可见"
kubectl describe services/kubernetes-bootcamp    "查看一个services的详细信息"
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')      "读取node port"
"此后可以使用minikube ip和NODEPORT访问pod中的服务"
```

    使用describe命令输出的结果中，可以看到deployment和pod的labels，pod的labels包含deployment的labels，在get命令中可以使用-l参数来指定label。

```
kubectl label pod $POD_NAME app=v1        "为pod新增一个label"
kubectl delete service -l run=kubernetes-bootcamp      "删除以指定label的服务"
```

### 伸缩部署

```
kubectl scale deployments/[deployment name] --replicas=4    "将pod扩展到4个，或者缩到4个"
"将应用外部可见之后，可以看到有四个内部endpoint，对应一个外部port"
curl $(minikube ip):$NODE_PORT     "每执行依次可以看到是不同的pod提供服务，已经自动负载均衡"
```

### 更新镜像

```
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2    "更新镜像版本"
kubectl rollout status deployments/kubernetes-bootcamp                     ""
kubectl rollout undo deployments/kubernetes-bootcamp                       "回退镜像版本到上一个版本（升级成功的版本）"
```

## Ingress
    An API object that manages external access to the services in a cluster, typically HTTP.Ingress can provide load balancing, SSL termination and name-based virtual hosting.提供虚拟主机配置

    Ingress Controller和Ingress：

    Ingress
        将Nginx的配置抽象成一个Ingress对象，每添加一个新的服务只需写一个新的Ingress的yaml文件即可
    Ingress Controller
        将新加入的Ingress转化成Nginx的配置文件并使之生效


## 其他

1. 替代镜像必须在minikube ssh中执行。
2. 使用私有仓库的镜像。

```
第一步：创建secret
kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>

第二步：在yml文件中指定secret
在spec.template.spec.imagePullSecrets段内填入SECRET名称。
```

## K8S开源项目

### [KubeOperator](https://github.com/KubeOperator/KubeOperator) 
1. KubeOperator 是一个开源项目，通过可视化 Web UI 在` VMware、Openstack 或者物理机`上规划、部署和运营生产级别的 Kubernetes 集群。支持内网离线环境、支持 GPU、内置应用商店，已通过 CNCF 的 Kubernetes 软件一致性认证。
2. KubeOperator 内置 KubeApps Plus 应用商店，以支撑各种基于 K8s 的应用场景，如：
* CI / CD 应用场景：GitLab、Jenkins、Harbor、Sonarqube、Argo CD 等；
* GPU / AI 应用场景：Tensorflow、PyTorch 等;
* Serverless 应用场景：Knative 等；
* 数据库应用场景：MySQL、Redis 等;
3. `资源需求`较高。

### [microk8s](https://microk8s.io/)
1. Microk8s由Canonical管理，是一种非弹性的、基于rails的单节点Kubernetes工具，主要面向`离线开发、原型设计和测试`。
