# 基于locust的分布式负载均衡测试

参考了[GoogleCloudPlatform/distributed-load-testing-using-kubernetes](https://github.com/GoogleCloudPlatform/distributed-load-testing-using-kubernetes/tree/master)和[rootsongjc
/
distributed-load-testing-using-kubernetes](https://github.com/rootsongjc/distributed-load-testing-using-kubernetes/tree/master)


## 部署 Web 应用
`sample-webapp` 目录下包含一个简单的 web 测试应用。我们将其构建为 docker 镜像，在 kubernetes 中运行。

在 kubernetes 上部署 sample-webapp。

```shell
git clone https://github.com/we1ru1/distributed-load-testing-using-kubernetes.git


cd kubernetes-config
kubectl apply -f sample-webapp-controller.yaml
kubectl apply -f sample-webapp-service.yaml
```

## 部署 Locust 的 Controller 和 Service
locust-master 和 locust-work 使用同样的 docker 镜像，修改 cotnroller 中 spec.template.spec.containers.env 字段中的 value 为你 sample-webapp service 的名字。

```
- name: TARGET_HOST
  value: http://sample-webapp:8000
```
### 创建 Controller Docker 镜像（可选）
这部分可以跳过，直接使用我的aliyun镜像仓库的镜像

```
cd kubernetes-config

# 构建镜像
docker build -t registry.cn-hangzhou.aliyuncs.com/we1ru1/locust-tasks:latest .
```
### 部署locust-master
```shell
kubectl apply -f locust-master-controller.yaml

kubectl apply -f locust-master-service.yaml
```
### 部署locust-worker
```shell
# 初始replicas设置的为1
kubectl apply -f locust-worker-controller.yaml
```

可以给worker进行扩容
```shell
$ kubectl scale --replicas=20 replicationcontrollers locust-worker
```

当然也可以通过WebUI：Dashboard - Workloads - Replication Controllers - **ServiceName** - Scale来扩容。



## 执行测试
打开`http://nodeIP:30089`（nodePort模式，此处`nodeIP`可以设置为集群内任意正常节点的IP）

0. img/1.png
**名词解析：**

_Number of users to simulate：设置模拟的用户总数_

_Hatch rate (users spawned/second)：每秒启动的虚拟用户数_

_Start swarming：执行locust脚本_



具体参数介绍参考：[Locust学习笔记4——UI界面介绍 - CSDN](https://blog.csdn.net/liudinglong1989/article/details/106954351)
