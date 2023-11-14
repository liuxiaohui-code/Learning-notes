# 术语

- BaaS：（Blockchain-as-a-Service）区块链即服务，本项目建设主体，即Blockchain-as-a-Service Platform，区块链即服务平台，以下简称BaaS平台；

- PaaS	:（Platform-as-a-Service）平台即服务，本项目指容器云平台；

- CBAS:（Cnaps Blockchain Access System）支付系统区块链统一接入服务，以下简称“区块链统一接入服务”。

# dind

```yaml
--- 
# peer 
env: 
 
  # - name: CORE_VM_ENDPOINT
  # value: "unix:///host/var/run/docker.sock"
  - name: CORE_VM_ENDPOINT
    value: "tcp://docker-service.default:2375"
   
--- 
# docker-service
apiVersion: v1
kind: Service
metadata:
  labels:
    app: docker-service
  name: docker-service
  namespace: default
spec:
  ports:
  - port: 2375
    protocol: TCP
    targetPort: 2375
  selector:
    app: dockerr
    
 --- 
 # deployment dind
 apiVersion: apps/v1
kind: Deployment
metadata:
  name: docker-dind
  namespace: default
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: dockerr
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: dockerr
    spec:
      containers:
      - command:
        - dockerd
        - --host=unix:///var/run/docker.sock
        - --host=tcp://0.0.0.0:2375
        image: docker:stable-dind
        imagePullPolicy: IfNotPresent
        name: docker
        ports:
        - containerPort: 2375
          protocol: TCP
        resources: {}
        securityContext:
          privileged: true
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

# 非dind

配置daemon.json
```
{
  "iptables": false,
  "dns": [
    "10.96.0.10",
    "100.100.1.1"
  ],
  "dns-search": [
    "default.svc.cluster.local",
    "svc.cluster.local"
  ],
  "dns-opts": [
    "ndots:8",
    "timeout:2",
    "attempts:10"
  ],
  "debug": true,
  "registry-mirrors": [
    "https://1sldomc8.mirror.aliyuncs.com"
  ],
  "experimental": true
}
```

# SDK

```go
configProvider := config.FromFile("configYaml_PATH")

func New(configProvider core.ConfigProvider, opts ...Option) (*FabricSDK, error) 

```



# Question

启动部署网络失败:

 生成更新配置信息失败:

 error encode update config to pb: 

error decoding input: 

*commonext.Config: 

error in PopulateFrom for field channel_group for message *commonext.Config: 

*commonext.DynamicChannelGroup: 

error in PopulateFrom for map field groups with key Consortiums for message *commonext.DynamicChannelGroup:

 *commonext.DynamicConsortiumsGroup:

 error in PopulateFrom for map field groups with key SampleConsortium for message *commonext.DynamicConsortiumsGroup:

 *commonext.DynamicConsortiumGroup:

 error in PopulateFrom for map field groups with key learn.gzccpc for message *commonext.DynamicConsortiumGroup: 

*commonext.DynamicConsortiumOrgGroup: 

error in PopulateFrom for map field values with key MSP for message *commonext.DynamicConsortiumOrgGroup:

 *commonext.DynamicConsortiumOrgConfigValue:

 expected field value for message *commonext.DynamicConsortiumOrgConfigValue to be assignable from map[string]interface {} but was not.  Is string

![image-20211104144618145](D:\study\Learning-notes\photo\image-20211104144618145.png)

![image-20211104144832676](C:\Users\liuxiaohui\AppData\Roaming\Typora\typora-user-images\image-20211104144832676.png)
