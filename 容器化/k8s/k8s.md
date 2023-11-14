# 基本组件

## 基本单元：pod

```
一个pod包含多个容器，IP相同，端口不同，内部容器通过localhost通信
```

## deployment与pod

```
创建deployment会创建pod,并保持pod的数量
pod 命名结构： deployment-ReplicaSets-pod
pod嵌入deployment中
deployment 支持滚动更新和混滚
```

## label

```
标签：key/value  (key 最长不超过63字节，value 可以为空，不差过253字节的字符串)
  - 不提供唯一性
  - Label Selector 选择同一组相同lable的对象
方式：
  - 等式：APP=NGINX和env!=pro
  - 集合，env in （Pro，qa）
  - 多个lable
```

## Service

- 微服务，pod与pod通信，
- IP
  - Node IP：node结点的IP地址；
  - pod IP，pod的IP地址（targetPort）；
  - cluster IP ：service 的 IP地址（port）；#pod 里面可以通过 clusterIP:Port访问
- type:
  - type: ClusterIP （默认）port:
  - type: NodePort      nodePort: 30000-32767   可以从集群的外部访问一个 NodePort 服务。
  - type: ExternalName   externalName: my.database.example.com  通过返回 CNAME 和它的值，可以将服务映射到 externalName 字段的内容（例如， foo.bar.example.com）。没有任何类型代理被创建，这只有 Kubernetes 1.7 或更高版本的 kube-dns 才支持。
  - LoadBalancer：使用云提供商的负载局衡器，可以向外部暴露服务。外部的负载均衡器可以路由到 NodePort 服务和 ClusterIP 服务，这个需要结合具体的云厂商进行操作。

## Pod

基本单元

## Master

​       集群的控制节点
组件：

```
- kube-apiserver：集群控制的入口，提供HTTP REST服务
- kube-controller-manager：所有资源对象的自动化控制中心
- kube-scheduler：负责Pod的调度
```

## Node

​		集群的工作节点，工作负载由Master节点分配，工作负载主要是运行容器应用

组件：

```
- kubelete：负责Pod的创建、启动、监控、重启、销毁等工作，同时与master节点协作，实现集群管理的基本功能
- kube-proxy：实现kubernetes Service的通信和负载均衡
- 运行容器化应用（pod）
```



## Namespace

一组资源和对象的抽象集合，常见的pods,services和deployments等都属于某一个namespace，而Node,PersistentVolumes等则不属于任何namespace

不同namespace空间的资源互不影响

# pod管理

## 静态pod

静态 Pod 直接由特定节点上的`kubelet`进程来管理，不通过 master 节点上的`apiserver`。无法与我们常用的控制器`Deployment`或者`DaemonSet`进行关联，它由`kubelet`进程自己来监控，当`pod`崩溃时重启该`pod`，`kubelete`也无法对他们进行健康检查。静态 pod 始终绑定在某一个`kubelet`，并且始终运行在同一个节点上。 `kubelet`会自动为每一个静态 pod 在 Kubernetes 的 apiserver 上创建一个镜像 Pod（Mirror Pod），因此我们可以在 apiserver 中查询到该 pod，但是不能通过 apiserver 进行控制（例如不能删除）。

创建静态 Pod 有两种方式：配置文件和 HTTP 两种方式

## hook

```yaml
lifecycle: #声明周期
  preStop: #（常用于优雅删除）
    exec:
      command: ["/usr/sbin/nginx","-s", "quit"] #优雅退出NGINX
  preStart:
    exec:
      command: ["/bin/bash","-c","echo 'hello' > /temp/hello.txt"]
```

## pod 健康检查

```sh
# liveness probe 存活探针：是否存活
# readiness probe 可读性探针：是否就绪

# 配置方式：
livenessProbe:
  exec: # 1.执行一段命令   
    command: []
    
  # httpGet: # 2. 检测某个http请求
  #   path: /healthz  # 返回状态码200-400之间为成功
  #   port: 8080
  
  # tcpSocket: # 3. kubelet尝试在指定端口打开容器的套接字，如果可以建立连接，容器就是健康的
  #   port: 8080 #端口检测
  initialDelaySSeconds: 5 #第一次执行探针的时候需要等待5s
  periodSeconds: 5 #每个五秒执行一次探针
  timeoutSeconds: 1 # 探测超时时间：默认1s,最小1秒
  successThreshold: 1 # 探测失败后，最少连续探测成功多少次才被认定为成功。默认是 1，但是如果是`liveness`则必须是 1。最小值是 1。
  failureThreshold: 1 # 探测成功后，最少连续探测失败多少次才被认定为失败。默认是 3，最小值是 1。
readinessProbe: #可读性探针
  # 同上
```

## 强制删除

```
kubectl delete pod pod-name --grace-period=0 --force
```

## 初始化容器

- 依赖，类似docker-compose depend on
- 初始化配置

```sh
initContainers:
  name: init-myservice
  image: busybox
  command: ["sh","-c","until nslookup myservice;do echo waiting for myservice;sleep 2; done"]
  # 等待服务 myservice 启动
```



## Replication Controller

- pod 管理；确保pod数量、健康（杀掉重建）、弹性伸缩、滚动升级
-  查看：`kubectl get rc`
- pod命名： rc-name - 随机数
- 热修改 `kubectl edit rc rcname`修改yaml文件
- 滚动更新`kubectl rolling-update rcname --image=nginx:1.7.9`，逐步替换，多个容器使用修改yaml
- 不支持回滚

```yaml
apiVersion: v1
kind: ReplicationController
metadata: 
  name: rc-demo
  labels:
    app: rc
spec:
  replicas: 3
  # name 自动生成
  selector: 
    app: rc # 筛选控制pod  默认与template labels一致
template:
  metadata: 
    name: rc-demo
    labels:
      app: rc
  spec: 
    containers:
    - name: nginx-demo
      image: nginx
      ports:
      - containerPort: 80
```

## Replica Set（RS）

- Replication Controller 的下一代，功能基本一致
- selector  支持 in 集合
- `kind: ReplicaSet`
- `kubectl get rc`
- 副本集，pod的抽象，用于解决pod的扩容和更新

## Deployment

- RC 升级版；rc功能、事件和状态查看、回滚、暂停和启动
- deployment:rs:pod = 1:多:多
- `kind: Deployment`
- 滚动升级 `kubectl apply -f .yaml --record=true`
- 继续滚动升级`kubectl rollout resume deployment deploymentName`
- 滚动升级状态`kubectl rollout status deployment deploymentName`
- 滚动升级暂停`kubectl rollout pause deployment deploymentName`
- 滚动升级记录`kubectl rollout history deployment deploymentName` 与 `kubectl get rs`一一对应
- 回滚上一个版本`kubectl rollout undo deployment deploymentName [--to-revision=int]`
- 每次rollout revision+1

```yaml
  revisionHistoryLimit: 15 # 回滚最大记录
  minReadySeconds: 5 # pod最少就绪3秒
  strategy: 
    type: RollingUpdate  # 滚动升级
    rollingUpdate: 
      maxSurge: 1
      maxUnavailable: 1
```

## DaemonSet

Daemon，就是用来部署守护进程的，`DaemonSet`用于在每个`Kubernetes`节点中将守护进程的副本作为后台进程运行，说白了就是在每个节点部署一个`Pod`副本，当节点加入到`Kubernetes`集群中，`Pod`会被调度到该节点上运行，当节点从集群只能够被移除后，该节点上的这个`Pod`也会被移除，当然，如果我们删除`DaemonSet`，所有和这个对象相关的`Pods`都会被删除。

正常情况下，`Pod`运行在哪个节点上是由`Kubernetes`的调度器策略来决定的，然而，由`DaemonSet`控制器创建的`Pod`实际上提前已经确定了在哪个节点上了（`Pod`创建时指定了`.spec.nodeName`），所以：

- `DaemonSet`并不关心一个节点的`unshedulable`字段，这个我们会在后面的调度章节和大家讲解的。
- `DaemonSet`可以创建`Pod`，即使调度器还没有启动，这点非常重要。

```yaml
# 在每个节点上部署一个Nginx Pod：(nginx-ds.yaml)
kind: DaemonSet
apiVersion: apps/v1
metadata:
  name: nginx-ds
  labels:
    k8s-app: nginx
spec:
  selector:
    matchLabels:
      k8s-app: nginx
  template:
    metadata:
      labels:
        k8s-app: nginx
    spec:
      containers:
      - image: nginx:1.7.9
        name: nginx
        ports:
        - name: http
          containerPort: 80
```



## StatefulSet

- 无状态服务（Stateless Service）：该服务运行的实例不会在本地存储需要持久化的数据，并且多个实例对于同一个请求响应的结果是完全一致的，比如前面我们讲解的`WordPress`实例，我们是不是可以同时启动多个实例，但是我们访问任意一个实例得到的结果都是一样的吧？因为他唯一需要持久化的数据是存储在`MySQL`数据库中的，所以我们可以说`WordPress`这个应用是无状态服务，但是`MySQL`数据库就不是了，因为他需要把数据持久化到本地。
- 有状态服务（Stateful Service）：就和上面的概念是对立的了，该服务运行的实例需要在本地存储持久化数据，比如上面的`MySQL`数据库，你现在运行在节点A，那么他的数据就存储在节点A上面的，如果这个时候你把该服务迁移到节点B去的话，那么就没有之前的数据了，因为他需要去对应的数据目录里面恢复数据，而此时没有任何数据。

`StatefulSet`类似于`ReplicaSet`，但是它可以处理`Pod`的启动顺序，为保留每个`Pod`的状态设置唯一标识，同时具有以下功能：

- 稳定的、唯一的网络标识符
- 稳定的、持久化的存储
- 有序的、优雅的部署和缩放
- 有序的、优雅的删除和终止
- 有序的、自动滚动更新

创建

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv001   #pv002
  labels:
    release: stable
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  hostPath:
    path: /tmp/data
    
#然后我们使用StatefulSet来创建一个 Nginx 的 Pod，对于这种类型的资源，我们一般是通过创建一个Headless Service类型的服务来暴露服务，将clusterIP设置为None就是一个无头的服务：（statefulset-demo.yaml）
---
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
    role: stateful

---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx
      role: stateful
  template:
    metadata:
      labels:
        app: nginx
        role: stateful
    spec:
      containers:
      - name: nginx
        image: cnych/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates: #volumeClaimTemplates，该属性会自动声明一个 pvc 对象和 pv 进行管理：
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```



## HPA自动扩缩

- Horizontal Pod Autoscaling (HPA)
- `kubectl autoscale`
- [https://github.com/kubernetes/heapster](https://github.com/kubernetes/heapster/)

```yaml
$ kubectl autoscale deployment hpa-nginx-deploy --cpu-percent=10 --min=1 --max=10
# 此命令创建了一个关联资源 hpa-nginx-deploy 的HPA，最小的 pod 副本数为1，最大为10。HPA会根据设定的 cpu使用率（10%）动态的增加或者减少pod数量。
$ kubectl get hpa

$ kubectl get hpa hpa-nginx-deploy -o yaml

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  creationTimestamp: 2017-06-29T08:04:08Z
  name: nginxtest
  namespace: default
  resourceVersion: "951016361"
  selfLink: /apis/autoscaling/v1/namespaces/default/horizontalpodautoscalers/nginxtest
  uid: 86febb63-5ca1-11e7-aaef-5254004e79a3
spec:
  maxReplicas: 5 //资源最大副本数
  minReplicas: 1 //资源最小副本数
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment //需要伸缩的资源类型
    name: nginxtest  //需要伸缩的资源名称
  targetCPUUtilizationPercentage: 50 //触发伸缩的cpu使用率
status:
  currentCPUUtilizationPercentage: 48 //当前资源下pod的cpu使用率
  currentReplicas: 1 //当前的副本数
  desiredReplicas: 2 //期望的副本数
  lastScaleTime: 2017-07-03T06:32:19Z
```

不过当前的`HPA`只有`CPU`使用率这一个指标，还不是很灵活的，在后面的课程中我们来根据我们自定义的监控来自动对`Pod`进行扩缩容。

## Job/CronJob

​		`Job`负责处理任务，即仅执行一次的任务，它保证批处理任务的一个或多个`Pod`成功结束。而`CronJob`则就是在`Job`上加上了时间调度。

```yaml
---
apiVersion: batch/v1
kind: Job
metadata:
  name: 
spec:
  template:
    metadata: 
      name: 
    spec:
      restartPolicy: Never # OnFailure 不支持Always
      containers:
      - name:
        image:
        command: []
#kubectl get jobs
#pod 状态：completed
```

`crontab`的格式如下：

> **分 时 日 月 星期 要运行的命令** 
>
> 第1列分钟0～59 第2列小时0～23 第3列日1～31 第4列月1～12 第5列星期0～7（0和7表示星期天） 第6列要运行的命令

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: 
spec:
  successfulJobHistoryLimit: 1  # 限制成功记录的个数
  FailedJobHistoryLimit: 10  # 限制失败记录的个数
  schedule: "*/1 * * * *"  # job调度 ：每分钟调度一次
  jobTemplate:
    #template:
      #metadata: 
        #name: 
      spec:
        restartPolicy: Never # OnFailure 不支持Always
        containers:
        - name:
          image:
          command: []
#kubectl get jobs
# 实例：kubectl run hello --schedule="*/1 * * * *" --restart=OnFailure --image=busybox -- /bin/sh -c "date; echo Hello from the Kubernetes cluster"
# kubectl delete cronjob hello
# 这将会终止正在创建的 Job。然而，运行中的 Job 将不会被终止，不会删除 Job 或 它们的 Pod。为了清理那些 Job 和 Pod，需要列出该 Cron Job 创建的全部 Job，然后删除它们
```



# 核心组件

```sh
----------------------Master
- etcd：保存集群状态，数据库
- API Server：资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现的机制；
- Sheduler：资源调度，将pod调度到对应的结点
- Controller Manager：维护集群的状态，比如：故障检测、自动扩展、滚动更新
------------------------Node
- kubelet：维护容器的生命周期，同时也负责Volume(CVI)和网络(CNI)的管理
- Container runtime:容器运行时,负责镜像管理以及pod和容器的真正运行(CRI)
- kube-proxy:负责为Service提供Cluster内部的服务发现和负载均衡
------------------推荐插件
- kube-dns:负责为整个集群提供dns服务
- Ingress Controller:为服务提供外网入口
- Heapster：提供资源监控
- dashboard:提供GUI
```

# 服务发现

## Dashbord

## kube-dns

kube-dns、dnsmasq-nanny、sidecar 这3个容器分别实现了什么功能?

- kubedns: kubedns 基于 SkyDNS 库，通过 apiserver 监听 Service 和 Endpoints 的变更事件同时也同步到本地 Cache，实现了一个实时的 Kubernetes 集群内 Service 和 Pod 的 DNS服务发现
- dnsmasq: dsnmasq 容器则实现了 DNS 的缓存功能(在内存中预留一块默认大小为 1G 的地方，保存当前最常用的 DNS 查询记录，如果缓存中没有要查找的记录，它会到 kubedns 中查询，并把结果缓存起来)，通过监听 ConfigMap 来动态生成配置
- sider: sidecar 容器实现了可配置的 DNS 探测，并采集对应的监控指标暴露出来供 prometheus 使用

![kube dns](\photo\kubedns.jpg)

DNS Pod 具有静态 IP 并作为 Kubernetes 服务暴露出来。该静态 IP 被分配后，kubelet 会将使用 `--cluster-dns = <dns-service-ip>`参数配置的 DNS 传递给每个容器。DNS 名称也需要域名，本地域可以使用参数`--cluster-domain = <default-local-domain>`在 kubelet 中配置。

按如上说明，具有**.acme.local**后缀的 DNS 请求被转发到 DNS 1.2.3.4。Google 公共 DNS 服务器 为上游查询提供服务。下表描述了具有特定域名的查询如何映射到它们的目标 DNS 服务器：

| 域名                                 | 响应查询的服务器                      |
| ------------------------------------ | ------------------------------------- |
| kubernetes.default.svc.cluster.local | kube-dns                              |
| foo.acme.local                       | 自定义 DNS (1.2.3.4)                  |
| widget.com                           | 上游 DNS (8.8.8.8, 8.8.4.4，其中之一) |

另外我们还可以为每个 Pod 设置 DNS 策略。 当前 Kubernetes 支持两种 Pod 特定的 DNS 策略：“Default” 和 “ClusterFirst”。 可以通过 dnsPolicy 标志来指定这些策略。

> 注意：**Default** 不是默认的 DNS 策略。如果没有显式地指定**dnsPolicy**，将会使用 **ClusterFirst**

- 如果 dnsPolicy 被设置为 “Default”，则名字解析配置会继承自 Pod 运行所在的节点。自定义上游域名服务器和存根域不能够与这个策略一起使用

- 如果 dnsPolicy 被设置为 “ClusterFirst”，这就要依赖于是否配置了存根域和上游 DNS 服务器

  - 未进行自定义配置：没有匹配上配置的集群域名后缀的任何请求，例如 “www.kubernetes.io”，将会被转发到继承自节点的上游域名服务器。

  - 进行自定义配置：如果配置了存根域和上游 DNS 服务器（类似于 前面示例 配置的内容），DNS 查询将基于下面的流程对请求进行路由：

    - 查询首先被发送到 kube-dns 中的 DNS 缓存层。

    - 从缓存层，检查请求的后缀，并根据下面的情况转发到对应的 DNS 上：

      - 具有集群后缀的名字（例如 “.cluster.local”）：请求被发送到 kubedns。
      - 具有存根域后缀的名字（例如 “.acme.local”）：请求被发送到配置的自定义 DNS 解析器（例如：监听在 1.2.3.4）。
      - 未能匹配上后缀的名字（例如 “widget.com”）：请求被转发到上游 DNS（例如：Google 公共 DNS 服务器，8.8.8.8 和 8.8.4.4）。
       ![kube dns](https://www.qikqiak.com/k8s-book/docs/images/dns.png)

      

### 域名格式

  我们前面说了如果我们建立的 Service 如果支持域名形式进行解析，就可以解决我们的服务发现的功能，那么利用 kubedns 可以将 Service 生成怎样的 DNS 记录呢？

  - 普通的 Service：会生成 servicename.namespace.svc.cluster.local 的域名，会解析到 Service 对应的 ClusterIP 上，在 Pod 之间的调用可以简写成 servicename.namespace，如果处于同一个命名空间下面，甚至可以只写成 servicename 即可访问
  - Headless Service：无头服务，就是把 clusterIP 设置为 None 的，会被解析为指定 Pod 的 IP 列表，同样还可以通过 podname.servicename.namespace.svc.cluster.local 访问到具体的某一个 Pod。

  > CoreDNS 实现的功能和 KubeDNS 是一致的，不过 CoreDNS 的所有功能都集成在了同一个容器中，在最新版的1.11.0版本中官方已经推荐使用 CoreDNS了，大家也可以安装 CoreDNS 来代替 KubeDNS，其他使用方法都是一致的：https://coredns.io/

```
我们进入到 Pod 中，查看/etc/resolv.conf
```

## CoreDNS

```
$ kubeadm init --feature-gates=CoreDNS=true
# 默认dns
# 使用基本与kubedns相同
```

## ingress 外

# 搭建

## Rancher

## kubeadm(测试)

```sh
$ vim /etc/hosts  #修改
#禁用防火墙
$ systemctl stop firewalld
$ systemctl disable firewalld
#禁用SELINUX
$ setenforce 0
$ cat /etc/selinux/config
#创建/etc/sysctl.d/k8s.conf文件，添加如下内容
$ vi /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
#执行如下命令使修改生效
$ sysctl -p /etc/sysctl.d/k8s.conf
#镜像
docker pull cnych/kube-apiserver-amd64:v1.10.0
docker pull cnych/kube-scheduler-amd64:v1.10.0
docker pull cnych/kube-controller-manager-amd64:v1.10.0
docker pull cnych/kube-proxy-amd64:v1.10.0
docker pull cnych/k8s-dns-kube-dns-amd64:1.14.8
docker pull cnych/k8s-dns-dnsmasq-nanny-amd64:1.14.8
docker pull cnych/k8s-dns-sidecar-amd64:1.14.8
docker pull cnych/etcd-amd64:3.1.12
docker pull cnych/flannel:v0.10.0-amd64
docker pull cnych/pause-amd64:3.1

docker tag cnych/kube-apiserver-amd64:v1.10.0 k8s.gcr.io/kube-apiserver-amd64:v1.10.0
docker tag cnych/kube-scheduler-amd64:v1.10.0 k8s.gcr.io/kube-scheduler-amd64:v1.10.0
docker tag cnych/kube-controller-manager-amd64:v1.10.0 k8s.gcr.io/kube-controller-manager-amd64:v1.10.0
docker tag cnych/kube-proxy-amd64:v1.10.0 k8s.gcr.io/kube-proxy-amd64:v1.10.0
docker tag cnych/k8s-dns-kube-dns-amd64:1.14.8 k8s.gcr.io/k8s-dns-kube-dns-amd64:1.14.8
docker tag cnych/k8s-dns-dnsmasq-nanny-amd64:1.14.8 k8s.gcr.io/k8s-dns-dnsmasq-nanny-amd64:1.14.8
docker tag cnych/k8s-dns-sidecar-amd64:1.14.8 k8s.gcr.io/k8s-dns-sidecar-amd64:1.14.8
docker tag cnych/etcd-amd64:3.1.12 k8s.gcr.io/etcd-amd64:3.1.12
docker tag cnych/flannel:v0.10.0-amd64 quay.io/coreos/flannel:v0.10.0-amd64
docker tag cnych/pause-amd64:3.1 k8s.gcr.io/pause-amd64:3.1

#docker安装
#kubeadm、kubelet、kubectl
```



## 二进制纯手动搭建（生产）



# 挂载

```sh
    volumeMounts:
    - name: 
      mountPath:
  valumes:
  - name: 
    hostPath: 
      path: 
      type:
  - name:
    emptyDir: {}  # 临时，生命周期随pod
```

## ConfigMap

- `ConfigMap`就给我们提供了向容器中注入配置信息的能力，不仅可以用来保存单个属性，也可以用来保存整个配置文件
- `kubectl get configmap`
- `kubectl create configmap`

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: cm-demo
  namespace: default
data:                       #数据
  data.1: hello             #单个属性
  data.2: world
  config: |                 #配置文件
    property.1=value-1
    property.2=value-2
    property.3=value-3
```

使用

```yaml
env: 
- name: **
  valueFrom:
    configMapKeyRef:
      name: **   #configMap 的name
      key: **
      
envFrom:
- configMapRef:
    name: ***
    
volumes:
  - name: ***
    configMap:
      name: ***
      items: 
        key: ***
        path: ***    #容器文件内部路径为 mountPath/path
```

> 另外需要注意的是，当`ConfigMap`以数据卷的形式挂载进`Pod`的时，这时更新`ConfigMap`（或删掉重建`ConfigMap`），`Pod`内挂载的配置信息会热更新。这时可以增加一些监测配置文件变更的脚本，然后`reload`对应服务。

## Secret

`Secret`有三种类型：

- Opaque：base64 编码格式的 Secret，用来存储密码、密钥等；但数据也可以通过base64 –decode解码得到原始数据，所有加密性很弱。
- kubernetes.io/dockerconfigjson：用来存储私有docker registry的认证信息。
  - `kubectl create secret docker-registry myregistry --docker-server=DOCKER_SEVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASS --docker-email=DOCKER_EMAIL`
- kubernetes.io/service-account-token：用于被`serviceaccount`引用，serviceaccout 创建时Kubernetes会默认创建对应的secret。Pod如果使用了serviceaccount，对应的secret会自动挂载到Pod目录`/run/secrets/kubernetes.io/serviceaccount`中。
- 创建 secret.yaml



```yaml
apiVerxion:
kind: Secret
metadata:
  name: ***
type: Opaque
data:
  username: ***   #***为base64编码之后的信息 
  password: ***

```

使用：

```yaml
env: 
- name: **
  valueFrom:
    secretKeyRef:
      name: **   #secret 的name
      key: **
      
volumes:
  - name: ***
    secret: 
      secretName: *** #secret name
      items:
        key: ***
        path: ***
```

## PV

PV 的全称是：PersistentVolume（持久化卷），是对底层的共享存储的一种抽象，PV 由管理员进行创建和配置，它和具体的底层的共享存储技术的实现方式有关，比如 Ceph、GlusterFS、NFS 等，都是通过插件机制完成与共享存储的对接。

PV 作为存储资源，主要包括存储能力、访问模式、存储类型、回收策略等关键信息，下面我们来新建一个 PV 对象，使用 nfs 类型的后端存储，1G 的存储空间，访问模式为 ReadWriteOnce，回收策略为 Recyle，对应的 YAML 文件如下：(pv1-demo.yaml)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name:  pv1
spec:
  capacity: 
    storage: 1Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    path: /data/k8s
    server: 10.151.30.57
```

Kubernetes 支持的 PV 类型有很多，比如常见的 Ceph、GlusterFs、NFS，甚至 HostPath也可以，不过 HostPath 我们之前也说过仅仅可以用于单机测试，更多的支持类型可以前往 [Kubernetes PV 官方文档](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)进行查看，因为每种存储类型都有各自的特点，所以我们在使用的时候可以去查看相应的文档来设置对应的参数。

```shell
$ kubectl get pv
```

### Capacity（存储能力）

一般来说，一个 PV 对象都要指定一个存储能力，通过 PV 的 **capacity**属性来设置的，目前只支持存储空间的设置，就是我们这里的 storage=1Gi，不过未来可能会加入 IOPS、吞吐量等指标的配置。

### AccessModes（访问模式）

AccessModes 是用来对 PV 进行访问模式的设置，用于描述用户应用对存储资源的访问权限，访问权限包括下面几种方式：

- ReadWriteOnce（RWO）：读写权限，但是只能被单个节点挂载
- ReadOnlyMany（ROX）：只读权限，可以被多个节点挂载
- ReadWriteMany（RWX）：读写权限，可以被多个节点挂载

> 注意：一些 PV 可能支持多种访问模式，但是在挂载的时候只能使用一种访问模式，多种访问模式是不会生效的。

下图是一些常用的 Volume 插件支持的访问模式： ![voluem-accessmodes](https://www.qikqiak.com/k8s-book/docs/images/access-modes.png)

### persistentVolumeReclaimPolicy（回收策略）

我这里指定的 PV 的回收策略为 Recycle，目前 PV 支持的策略有三种：

- Retain（保留）- 保留数据，需要管理员手工清理数据
- Recycle（回收）- 清除 PV 中的数据，效果相当于执行 rm -rf /thevoluem/*
- Delete（删除）- 与 PV 相连的后端存储完成 volume 的删除操作，当然这常见于云服务商的存储服务，比如 ASW EBS。

不过需要注意的是，目前只有 NFS 和 HostPath 两种类型支持回收策略。当然一般来说还是设置为 Retain 这种策略保险一点。

### 状态

一个 PV 的生命周期中，可能会处于4中不同的阶段：

- Available（可用）：表示可用状态，还未被任何 PVC 绑定
- Bound（已绑定）：表示 PVC 已经被 PVC 绑定
- Released（已释放）：PVC 被删除，但是资源还未被集群重新声明
- Failed（失败）： 表示该 PV 的自动回收失败

这就是 PV 的声明方法，下节课我们来和大家一起学习下 PVC 的使用方法。

## PVC

PVC 的全称是：PersistentVolumeClaim（持久化卷声明），PVC 是用户存储的一种声明，PVC 和 Pod 比较类似，Pod 消耗的是节点，PVC 消耗的是 PV 资源，Pod 可以请求 CPU 和内存，而 PVC 可以请求特定的存储空间和访问模式。对于真正使用存储的用户不需要关心底层的存储实现细节，只需要直接使用 PVC 即可。

新建一个数据卷声明，我们来请求 1Gi 的存储容量，访问模式也是 ReadWriteOnce，YAML 文件如下：(pvc-nfs.yaml)

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  selector: # 省略则自动匹配合适的PV
    matchLabels:
      app: nfs
```

有的同学可能会觉得很奇怪，我们并没有在 pvc-nfs 中指定关于 pv 的什么标志，它们之间是怎么就关联起来了的呢？其实这是系统自动帮我们去匹配的，他会根据我们的声明要求去查找处于 Available 状态的 PV，如果没有找到的话那么我们的 PVC 就会一直处于 Pending 状态，找到了的话当然就会把当前的 PVC 和目标 PV 进行绑定，这个时候状态就会变成 Bound 状态了。

挂载

```yaml
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
      volumes:
      - name: www
        persistentVolumeClaim:
          claimName: pvc-nfs
```

## StrorageClass

但是通过 PVC 请求到一定的存储空间也很有可能不足以满足应用对于存储设备的各种需求，而且不同的应用程序对于存储性能的要求可能也不尽相同，比如读写速度、并发性能等，为了解决这一问题，Kubernetes 又为我们引入了一个新的资源对象：StorageClass，通过 StorageClass 的定义，管理员可以将存储资源定义为某种类型的资源，比如快速存储、慢速存储等，用户根据 StorageClass 的描述就可以非常直观的知道各种存储资源的具体特性了，这样就可以根据应用的特性去申请合适的存储资源了。

动态 PV，要使用 StorageClass，我们就得安装对应的自动配置程序。

比如我们这里存储后端使用的是 **nfs**，那么我们就需要使用到一个 nfs-client 的自动配置程序，我们也叫它 Provisioner，这个程序使用我们已经配置好的 nfs 服务器，来自动创建持久卷，也就是自动帮我们创建 PV。

- 自动创建的 PV 以`${namespace}-${pvcName}-${pvName}`这样的命名格式创建在 NFS 服务器上的共享数据目录中
- 而当这个 PV 被回收后会以`archieved-${namespace}-${pvcName}-${pvName}`这样的命名格式存在 NFS 服务器上。

当然在部署`nfs-client`之前，我们需要先成功安装上 nfs 服务器，前面的课程中我们已经过了，服务地址是**10.151.30.57**，共享数据目录是**/data/k8s/**，然后接下来我们部署 nfs-client 即可，我们也可以直接参考 nfs-client 的文档：https://github.com/kubernetes-retired/external-storage/blob/master/nfs-client/README.md，进行安装即可。

**第一步**：配置 Deployment，将里面的对应的参数替换成我们自己的 nfs 配置（nfs-client.yaml）

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: nfs-client-provisioner
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: fuseim.pri/ifs
            - name: NFS_SERVER
              value: 10.151.30.57
            - name: NFS_PATH
              value: /data/k8s
      volumes:
        - name: nfs-client-root
          nfs:
            server: 10.151.30.57
            path: /data/k8s
```

**第二步**：将环境变量 NFS_SERVER 和 NFS_PATH 替换，当然也包括下面的 nfs 配置，我们可以看到我们这里使用了一个名为 nfs-client-provisioner 的`serviceAccount`，所以我们也需要创建一个 sa，然后绑定上对应的权限：（nfs-client-sa.yaml）

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner

---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["list", "watch", "create", "update", "patch"]
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["create", "delete", "get", "list", "watch", "patch", "update"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
```

我们这里新建的一个名为 nfs-client-provisioner 的`ServiceAccount`，然后绑定了一个名为 nfs-client-provisioner-runner 的`ClusterRole`，而该`ClusterRole`声明了一些权限，其中就包括对`persistentvolumes`的增、删、改、查等权限，所以我们可以利用该`ServiceAccount`来自动创建 PV。

**第三步**：nfs-client 的 Deployment 声明完成后，我们就可以来创建一个`StorageClass`对象了：（nfs-client-class.yaml）

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: course-nfs-storage
provisioner: fuseim.pri/ifs # or choose another name, must match deployment's env PROVISIONER_NAME'
```

我们声明了一个名为 course-nfs-storage 的`StorageClass`对象，注意下面的`provisioner`对应的值一定要和上面的`Deployment`下面的 PROVISIONER_NAME 这个环境变量的值一样。

上面把`StorageClass`资源对象创建成功了，接下来我们来通过一个示例测试下动态 PV，首先创建一个 PVC 对象：(test-pvc.yaml)

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: test-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Mi
```

我们这里声明了一个 PVC 对象，采用 ReadWriteMany 的访问模式，请求 1Mi 的空间，但是我们可以看到上面的 PVC 文件我们没有标识出任何和 StorageClass 相关联的信息，那么如果我们现在直接创建这个 PVC 对象能够自动绑定上合适的 PV 对象吗？显然是不能的(前提是没有合适的 PV)，我们这里有两种方法可以来利用上面我们创建的 StorageClass 对象来自动帮我们创建一个合适的 PV:

- 第一种方法：在这个 PVC 对象中添加一个声明 StorageClass 对象的标识，这里我们可以利用一个 annotations 属性来标识，如下：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: test-pvc
  annotations:
    volume.beta.kubernetes.io/storage-class: "course-nfs-storage"
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Mi
```

- 第二种方法：我们可以设置这个 course-nfs-storage 的 StorageClass 为 Kubernetes 的默认存储后端，我们可以用 kubectl patch 命令来更新：

```shell
$ kubectl patch storageclass course-nfs-storage -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

上面这两种方法都是可以的，当然为了不影响系统的默认行为，我们这里还是采用第一种方法，直接创建即可

# RBAC

`RBAC` - 基于角色的访问控制。

`RBAC`使用`rbac.authorization.k8s.io` API Group 来实现授权决策，允许管理员通过 Kubernetes API 动态配置策略，要启用`RBAC`，需要在 apiserver 中添加参数`--authorization-mode=RBAC`，如果使用的`kubeadm`安装的集群，1.6 版本以上的都默认开启了`RBAC`，可以通过查看 Master 节点上 apiserver 的静态`Pod`定义文件：

```shell
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml 
...
    - --authorization-mode=Node,RBAC
...
```

如果是二进制的方式搭建的集群，添加这个参数过后，记得要重启 apiserver 服务。

`Kubernetes`有一个很基本的特性就是它的[所有资源对象都是模型化的 API 对象](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)，允许执行 CRUD(Create、Read、Update、Delete)操作(也就是我们常说的增、删、改、查操作)，比如下面的这下资源：

- Pods
- ConfigMaps
- Deployments
- Nodes
- Secrets
- Namespaces

上面这些资源对象的可能存在的操作有：

- create
- get
- delete
- list
- update
- edit
- watch
- exec

在更上层，这些资源和 API Group 进行关联，比如`Pods`属于 Core API Group，而`Deployements`属于 apps API Group，要在`Kubernetes`中进行`RBAC`的管理，除了上面的这些资源和操作以外，我们还需要另外的一些对象：

- Rule：规则，规则是一组属于不同 API Group 资源上的一组操作的集合
- Role 和 ClusterRole：角色和集群角色，这两个对象都包含上面的 Rules 元素，二者的区别在于，在 Role 中，定义的规则只适用于单个命名空间，也就是和 namespace 关联的，而 ClusterRole 是集群范围内的，因此定义的规则不受命名空间的约束。另外 Role 和 ClusterRole 在`Kubernetes`中都被定义为集群内部的 API 资源，和我们前面学习过的 Pod、ConfigMap 这些类似，都是我们集群的资源对象，所以同样的可以使用我们前面的`kubectl`相关的命令来进行操作
- Subject：主题，对应在集群中尝试操作的对象，集群中定义了3种类型的主题资源：
  - User Account：用户，这是有外部独立服务进行管理的，管理员进行私钥的分配，用户可以使用 KeyStone或者 Goolge 帐号，甚至一个用户名和密码的文件列表也可以。对于用户的管理集群内部没有一个关联的资源对象，所以用户不能通过集群内部的 API 来进行管理
  - Group：组，这是用来关联多个账户的，集群中有一些默认创建的组，比如cluster-admin
  - Service Account：服务帐号，通过`Kubernetes` API 来管理的一些用户帐号，和 namespace 进行关联的，适用于集群内部运行的应用程序，需要通过 API 来完成权限认证，所以在集群内部进行权限操作，我们都需要使用到 ServiceAccount，这也是我们这节课的重点
- RoleBinding 和 ClusterRoleBinding：角色绑定和集群角色绑定，简单来说就是把声明的 Subject 和我们的 Role 进行绑定的过程(给某个用户绑定上操作的权限)，二者的区别也是作用范围的区别：RoleBinding 只会影响到当前 namespace 下面的资源操作权限，而 ClusterRoleBinding 会影响到所有的 namespace。

# Q-A

```
生成token
https://blog.csdn.net/weixin_38320674/article/details/107328982
```

pod 节点无法访问本地hosts

```
挂载/etc/hosts到pod容器内部
```

ca

```
/etc/kubernetes/pki/etcd
```

clusterip

```
/etc/resolv.conf 
```



# 配置文件示例

​		在同一个 yaml 中不同组件配置通过`---`分割；

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ops-nginx-api  # Deployment 对象的名称，与应用名称保持一致
  namespace: default    #命名空间
  labels:
    appName: ops-nginx-api  # 应用名称
spec:
  selector:
    matchLabels:
      app: ops-nginx-api  #app 标签名称
  replicas: 5 #Pod
  strategy: #部署策略更多策略 1.https://www.qikqiak.com/post/k8s-deployment-strategies/
    type: RollingUpdate #其他类型如下 1.重建(Recreate) 开发环境使用 2.RollingUpdate(滚动更新)
    rollingUpdate:
       maxSurge: 25%        #一次可以添加多少个Pod
       maxUnavailable: 25%  #滚动更新期间最大多少个Pod不可用
  template:
    metadata:
      labels:
        app: 'ops-nginx-api'
    spec:
      terminationGracePeriodSeconds: 120 #优雅关闭时间，这个时间内优雅关闭未结束，k8s 强制 kill
      affinity:
        podAntiAffinity:  # pod反亲和性，尽量避免同一个应用调度到相同node
          preferredDuringSchedulingIgnoredDuringExecution: #硬需求 1.preferredDuringSchedulingIgnoredDuringExecution 软需求
            - weight: 100
              #weight 字段值的 范围是 1-100。 对于每个符合所有调度要求（资源请求、RequiredDuringScheduling 亲和性表达式等） 的节点，调度器将遍历该字段的元素来计算总和，并且如果节点匹配对应的 MatchExpressions，则添加“权重”到总和。 然后将这个评分与该节点的其他优先级函数的评分进行组合。 总分最高的节点是最优选的。
              podAffinityTerm:
                labelSelector:
                  matchExpressions:
                    - key: app
                      operator: In
                      values:
                        - ops-nginx-api
                topologyKey: "kubernetes.io/hostname"

      initContainers:  #初始化容器
        - name: sidecar-sre #init 容器名称
          image: breaklinux/sidecar-sre:201210129  #docker hup仓库镜像
          imagePullPolicy: IfNotPresent  #镜像拉取策略 1.IfNotPresent如果本地存在镜像就优先使用本地镜像。2.Never直接不再去拉取镜像了,使用本地的.如果本地不存在就报异常了。
          #3.imagePullPolicy 未被定义为特定的值，默认值设置为 Always 本地是否存在都会去仓库拉取镜像.
          env:
            #Downward API官网示例 https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/
            - name: CHJ_NODE_IP
              valueFrom:
                fieldRef:  #这两种呈现 Pod 和 Container 字段的方式统称为 Downward API。
                  fieldPath: status.hostIP #获取pod 所在node IP地址设置为CHJ_NODE_IP

            - name: CHJ_POD_IP  #变量名称
              valueFrom:
                fieldRef:
                  fieldPath: status.podIP #获取pod自身ip地址设置为CHJ_POD_IP变量名称

            - name: CHJ_APP_NAME   #应用名称EVN
              value: 'ops-nginx-api' #环境变量值

          command: ["/bin/sh","-c"]  #shell 执行
          args: ["mkdir -p /scratch/.sidecar-sre && cp /sidecar/post-start.sh /scratch/.sidecar-sre/post-start.sh && /scratch/.sidecar-sre/post-start.sh"]

          volumeMounts: #挂载日志卷和存在的空镜像
            - name: log-volume
              mountPath: /tmp/data/log/ops-nginx-api  #容器内挂载镜像路径
            - name: sidecar-sre
              mountPath: /scratch  #空镜像

          resources:  #qos 设置
            limits:
              cpu: 100m #pod 占单核cpu 1/10
              memory: 100Mi #内存100M
        #hostPath 卷能将主机节点文件系统上的文件或目录挂载到你的 Pod 中。 虽然这不是大多数 Pod 需要的，但是它为一些应用程序提供了强大的逃生舱。
        volumes:
          - name: log-volume  #卷名称
            hostPath:         #卷类型详细见:https://kubernetes.io/zh/docs/concepts/storage/volumes/
              path: /data/logs/prod/ops-nginx-api  #宿主机存在的目录路径
              type: DirectoryOrCreate #如果在给定路径上什么都不存在，那么将根据需要创建空目录，权限设置为 0755，具有与 kubelet 相同的组和属主信息
          - name: sidecar-sre  #
            emptyDir: {} #emptyDir 卷的存储介质（磁盘、SSD 等）是由保存 kubelet 数据的根目录 （通常是 /var/lib/kubelet）的文件系统的介质确定。

      containers:
        - name: ops-nginx-api # 容器名称，与应用名称保持一致
          image: breaklinux/op-flask-api:v1 #遵守镜像命名规范
          imagePullPolicy: Always  #镜像拉取策略 1.IfNotPresent如果本地存在镜像就优先使用本地镜像。2.Never直接不再去拉取镜像了,使用本地的.如果本地不存在就报异常了。
          #3.imagePullPolicy 未被定义为特定的值，默认值设置为 Always 本地是否存在都会去仓库拉取镜像.
          lifecycle: #Kubernetes 支持 postStart 和 preStop 事件。 当一个容器启动后，Kubernetes 将立即发送 postStart 事件；在容器被终结之前， Kubernetes 将发送一个 preStop 事件。
            postStart: #容器创建成功后，运行前的任务，用于资源部署、环境准备等。
              exec:
                command:
                  - /bin/sh
                  - -c
                  - echo 'Hello from the postStart handler' >> /var/log/nginx/message
            preStop: #在容器被终止前的任务，用于优雅关闭应用程序、通知其他系统等等
              exec:
                command:
                  - sh
                  - -c
                  - sleep 30

          livenessProbe:  #存活探针器配置
            failureThreshold: 3 #处于成功时状态时，探测操作至少连续多少次的失败才被视为检测不通过，显示为#failure属性.默认值为3,最小值为 1,存活探测情况下的放弃就意味着重新启动容器。
            httpGet: #1.存活探针器三种方式 1.cmd命令方式进行探测 2.http 状态码方式 3.基于tcp端口探测
              path: /healthy #k8s源码中healthz 实现 https://github.com/kubernetes/kubernetes/blob/master/test/images/agnhost/liveness/server.go
              port: 8080    #应用程序监听端口
            initialDelaySeconds: 600 #存活性探测延迟时长,即容器启动多久之后再开始第一次探测操作,显示为delay属性.默认值为0，即容器启动后立刻便开始进行探测.
            periodSeconds: 10  #执行探测的时间间隔（单位是秒）。默认是 10 秒。最小值是 1秒,过高的频率会对Pod对象带来较大的额外开销,而过低的频率又会使得对错误的反应不及时.
            successThreshold: 1 #处于失败状态时,探测操作至少连续多少次的成功才被人为是通过检测，显示为#success属性,默认值为1，最小值也是1
            timeoutSeconds: 3 #存活性探测的超时时长,显示为timeout属性,默认值1s,最小值也是1s

          readinessProbe:  #定义就绪探测器
            failureThreshold: 3 #处于成功时状态时，探测操作至少连续多少次的失败才被视为检测不通过，显示为#failure属性.默认值为3,最小值为  就绪探测情况下的放弃 Pod 会被打上未就绪的标签.

            tcpSocket: # 1.就绪探针三种方式 1.cmd命令方式进行探测 2.http 状态码方式 3.基于tcp端口探测
              port: 8080 #应用程序监听端口
            initialDelaySeconds: 10 #执行探测的时间间隔（单位是秒）。默认是 10 秒。最小值是 1秒,过高的频率会对Pod对象带来较大的额外开销,而过低的频率又会使得对错误的反应不及时.
            periodSeconds: 10  #执行探测的时间间隔（单位是秒）。默认是 10 秒。最小值是 1秒,过高的频率会对Pod对象带来较大的额外开销,而过低的频率又会使得对错误的反应不及时
            successThreshold: 1 #处于失败状态时,探测操作至少连续多少次的成功才被人为是通过检测，显示为#success属性,默认值为1，最小值也是1
            timeoutSeconds: 3  #存活性探测的超时时长,显示为timeout属性,默认值1s,最小值也是1s

          ports:
            - containerPort: 19201 #应用监听的端口
              protocol: TCP #协议 tcp和 udp

          env:  #应用配置中心环境变量
            - name: ENV
              value: test
            - name: apollo.meta
              value: http://saos-apollo-config-service:8080
            - name: CHJ_APP_NAME
              value: 'ops-nginx-api'
            - name: LOG_BASE
              value: '/chj/data/log'
            - name: RUNTIME_CLUSTER
              value: 'default'

          #Request: 容器使用的最小资源需求，作为容器调度时资源分配的判断依赖。只有当节点上可分配资源量>=容器资源请求数时才允许将容器调度到该节点。但Request参数不限制容器的最大可使用资源。
          #Limit: 容器能使用资源的资源的最大值，设置为0表示使用资源无上限。
          resources:  #qos限制 1.QoS 主要分为Guaranteed、Burstable 和 Best-Effort三类，优先级从高到低
            requests:
              memory: 4096Mi #内存4G
              cpu: 500m #cpu 0.5
            limits:
              memory: 4096Mi
              cpu: 500m

          volumeMounts: #挂载苏宿主机目录到制定
            - name: log-volume  # sidecar-sre
              mountPath: /tmp/data/log/ops-nginx-api #该目录作为程序日志sidecar路径收集
            - name: sidecar-sre
              mountPath: /chj/data  #苏主机挂载目录

      imagePullSecrets:  #在 Pod 中设置 ImagePullSecrets 只有提供自己密钥的 Pod 才能访问私有仓库
        - name: lixiang-images-pull  #镜像Secrets需要在集群中手动创建
```

## pod.yaml

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190418165853331.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h6bTU3MDg3OTY=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190418165928901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h6bTU3MDg3OTY=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190418165954556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h6bTU3MDg3OTY=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190418170030679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h6bTU3MDg3OTY=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190418170051630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h6bTU3MDg3OTY=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190418170102754.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h6bTU3MDg3OTY=,size_16,color_FFFFFF,t_70)



## service.yaml

![img](\photo\service.yaml.png)

# 学习资料

1. Kubernetes指南:[https://kubernetes.feisky.xyz/](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.feisky.xyz%2F) 
2. Kubernetes官网:[https://kubernetes.io/](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.io%2F) 
3. Kubernetes中文社区:[https://www.kubernetes.org.cn/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.kubernetes.org.cn%2F) 
4. Kubernetes中文文档:[https://kubernetes.io/zh/docs/home/](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.io%2Fzh%2Fdocs%2Fhome%2F) 
5. Kubernetes官方文档:[https://kubernetes.io/docs/home/](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.io%2Fdocs%2Fhome%2F) 
6. 书籍：k8s 指南
7. yaml 文档 https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.22/#-strong-api-groups-strong-
8. 博客 https://www.qikqiak.com/k8s-book/
9. 网页运行环境 www.katacoda.com/courses/kubernetes
10. 视频学习  https://www.bilibili.com/video/BV1aL4y1h7EX?p=10

```
tar --transform 's,^,brilliance.com.cn/baas/chaincodes/totalizer,' -czf  totalizer.tar.gz totalizer
```

# Questions

## 1. Pod容器内部无法ping 通ClusterIP（或ServiceName）

https://blog.csdn.net/Urms_handsomeyu/article/details/106294085

