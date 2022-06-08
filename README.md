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

### 1. Login Docker Hub

```shell
docker login -u <username> -p <password>
```

### 2. Install NFS

For detailed tutorial, turn to [Link]( https://qizhanming.com/blog/2018/08/08/how-to-install-nfs-on-centos-7 )，the "master" of this project is nfs server with all the nodes being nfs clients. Mount directory `/var/nfs/data/`

### 2. Install OpenFaaS

Check details from [OpenFaaS Official Document](https://docs.openfaas.com/deployment/kubernetes/). 3 installation methods are provided. We recommend to use helm (arkade is not mature enough; though yaml files support customized installation, it is over-complicated). 

### 3. Clone Repository

```
git clone https://gitee.com/akasakaisami/train-ticket-serverless.git
cd Serverless_trainticket/
```

### 4. Config and Deploy Global Variables

use the shell in master node

```shell
MASTER_ID=<master_ip_address>
NODE01_ID=<node01_ip_address>
NODE02_ID=<node02_ip_address>
NODE03_ID=<node03_ip_address>
DOCKER_USERNAME=<docker_username>
```

### 5. Execute Database Auto-deploy Script File

```shell
# deploy database
sed -i s/master_ip_address/$MASTER_ID/ part01_DataBaseDeployment.sh
sed -i s/docker_username/$DOCKER_USERNAME/ part01_DataBaseDeployment.sh

chmod u+x part01_DataBaseDeployment.sh
./part01_DataBaseDeployment.sh
```

Run `kubectl get pods` and `kubectl get pods -n openfaas-fn` 

Wait until all the database initialization function Pods are `Ready` and execute the following.

```shell
# Data content initialization
sed -i s/10.141.212.140/$MASTER_ID/ part01_DataInitiation.sh

chmod u+x part01_DataInitiation.sh
./part01_DataInitiation.sh
```

### 6. Execute Backend Auto-deploy Script File

```shell
# BaaS Service Deployment
chmod u+x part02_BaaSServices.sh
./part02_BaaSServices.sh
```

```shell
# FaaS Function Deployment
sed -i s/master_ip_address/$MASTER_ID/ part02_FaaSFunctions.sh
sed -i s/docker_username/$DOCKER_USERNAME/ part02_FaaSFunctions.sh

chmod u+x part02_FaaSFunctions.sh
./part02_FaaSFunctions.sh
```

### 7. Execute Frontend Auto-deploy Script File

```shell
# Frontend deployment
chmod u+x part03_Frontend.sh
./part03_Frontend.sh
```

### 8. Run `kubectl get pods --all-namespaces` 

Wait until all Pods are `Ready`.

### 9. Visit Serverless TrainTicket Homepage at http://[Node-IP]:32677


