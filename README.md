# Serverless TrainTicket

[TrainTicket](https://github.com/FudanSELab/train-ticket) [TrainTicket](https://github.com/FudanSELab/train-ticket) is a open-sourced microservice benchmarking system developed by CodeWisdom research group from Fudan University based on industrial microservice practice. It is a train ticket ordering system based on microservice architecture, which encompasses 41 different microservices. This project adopts open functions framework, [OpenFaaS](https://github.com/openfaas/faas), based on serverless architecture, extracts and transforms the ticket bokking module with high concurrency in the open-sourced microservice system, TrainTicket, which is deployed in Kubernetes clusters. 

The main adopted development frameworks include:

- Java - OpenFaaS、OkHttp、*Spring Boot
- DB - MongoDB、MongoBD JDBC


## Quick Start

This project, based on Kubernetes clusters, adopts open functions framework, OpenFaas, to deploy our Serverless TrainTicket system.

### Prerequisites

Because this project uses Kubernetes to build OpenFaas Serverless platform, you need at least 2 servers to build Kubernetes clusters. [Tutorials for Cluster Deployment (in Chinese)](https://blog.csdn.net/lbw520/article/details/96446272)


#### Server System Requirements

- CPU and RAM: duel-core, 4GB+
- OS: Various Linux on x86_64, including CentOS, Federa, Ubuntu, etc., but kernal should be 3.10 or above.
- Container： Normally use Docker as container

(Translation on going ... )

### 1. 登录Docker Hub

```shell
docker login -u <username> -p <password>
```

### 2. 安装NFS

详细步骤参考[该链接](https://qizhanming.com/blog/2018/08/08/how-to-install-nfs-on-centos-7)，本项目中master节点为nfs服务端、所有node节点为nfs客户端，挂载路径为`/var/nfs/data/`。

### 2. 安装OpenFaaS

详细步骤参考[OpenFaaS官方文档](https://docs.openfaas.com/deployment/kubernetes/)。官方提供了三种安装OpenFaaS的方法，建议使用 helm（arkade不够成熟，yaml文件支持定制自定义安装方案但过于繁琐）。

### 3. 克隆项目仓库

```sh
git clone https://gitee.com/akasakaisami/train-ticket-serverless.git
cd Serverless_trainticket/
```

### 4. 配置部署需要的全局变量

master节点的shell中输入

```shell
MASTER_ID=<master_ip_address>
NODE01_ID=<node01_ip_address>
NODE02_ID=<node02_ip_address>
NODE03_ID=<node03_ip_address>
DOCKER_USERNAME=<docker_username>
```

### 5. 执行数据库自动部署脚本文件

```shell
# 部署数据库
sed -i s/master_ip_address/$MASTER_ID/ part01_DataBaseDeployment.sh
sed -i s/docker_username/$DOCKER_USERNAME/ part01_DataBaseDeployment.sh

chmod u+x part01_DataBaseDeployment.sh
./part01_DataBaseDeployment.sh
```

运行 `kubectl get pods`和`kubectl get pods -n openfaas-fn` 等待所有数据库初始化函数 Pods 都是 Ready 状态，再运行以下命令

```shell
# 数据内容初始化
sed -i s/10.141.212.140/$MASTER_ID/ part01_DataInitiation.sh

chmod u+x part01_DataInitiation.sh
./part01_DataInitiation.sh
```

### 6. 执行后端自动部署脚本文件

```shell
# BaaS服务部署
chmod u+x part02_BaaSServices.sh
./part02_BaaSServices.sh
```

```shell
# FaaS函数部署
sed -i s/master_ip_address/$MASTER_ID/ part02_FaaSFunctions.sh
sed -i s/docker_username/$DOCKER_USERNAME/ part02_FaaSFunctions.sh

chmod u+x part02_FaaSFunctions.sh
./part02_FaaSFunctions.sh
```

### 7. 执行前端自动部署脚本文件

```shell
# 前端部署
chmod u+x part03_Frontend.sh
./part03_Frontend.sh
```

### 8. 运行 `kubectl get pods --all-namespaces` 等待所有 Pods 都是 Ready 状态

### 9. 访问 Serverless TrainTicket 主页 http://[Node-IP]:32677


