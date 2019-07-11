# 了解kubernetes的基本概念
*****************************
## 学习 Kubernetes 术语
和其它技术一样，Kubernetes 也会采用一些专用的词汇，这可能会对初学者理解和掌握这项技术造成一定的障碍。为了帮助您了解 Kubernetes，我们在下面来解释一些常用术语。

**主机（Master）**： 用于控制 Kubernetes 节点的计算机。所有任务分配都来自于此。

**节点（Node）**：负责执行请求和所分配任务的计算机。由 Kubernetes 主机负责对节点进行控制。

**容器集（Pod**）：被部署在单个节点上的，且包含一个或多个容器的容器组。同一容器集中的所有容器共享同一个 IP 地址、IPC、主机名称及其它资源。容器集会将网络和存储从底层容器中抽象出来。这样，您就能更加轻松地在集群中移动容器。

**复制控制器（Replication controller）**：用于控制应在集群某处运行的完全相同的容器集副本数量。

**服务（Service**）：将工作内容与容器集分离。Kubernetes 服务代理会自动将服务请求分发到正确的容器集——无论这个容器集会移到集群中的哪个位置，甚至可以被替换掉。

**Kubelet**：运行在节点上的服务，可读取容器清单（container manifest），确保指定的容器启动并运行。

**kubectl**： Kubernetes 的命令行配置工具。

**Kubernetes 架构**

![](https://feisky.gitbooks.io/kubernetes/architecture/images/14791969222306.png)

![](https://feisky.gitbooks.io/kubernetes/architecture/images/14791969311297.png)
---------------------------------------------------------------------------------------
## 用Minikube创建一个集群
### 目标  

- 了解什么是Kubernetes 集群
- 了解什么是Minikube
- 用一个在线终端开始一个Kubernetes集群

### Kubernetes 集群
**Kubernetes协调一个高度可用的计算机集群，这些计算机连接起来作为一个单元工作。**Kubernetes中的抽象允许您将容器化应用程序部署到集群，而无需将它们专门绑定到单个计算机上。为了利用这种新的部署模型，应用程序需要以一种将它们与单个主机分离的方式打包：它们需要容器化。比起过去的部署模型容器化应用程序更加灵活和可用，过去的部署模型中应用程序需要安装到指定的计算机上，程序包深度集成于主机中。**Kubernetes以更有效的方式自动化跨集群分发和调度应用程序容器**。Kubernetes是一个开源平台，可以投入生产。  

Kubernetes集群包含两种类型的资源：  

- Master协调集群  
- Nodes是运行应用程序的工作者


集群图

![](https://d33wubrfki0l68.cloudfront.net/99d9808dcbf2880a996ed50d308a186b5900cec9/40b94/docs/tutorials/kubernetes-basics/public/images/module_01_cluster.svg)

**主机负责管理集群。**主机协调集群中的所有活动，例如调度应用程序，维护应用程序的所需状态，扩展应用程序以及推出新更新。 

**节点是VM或物理计算机，充当Kubernetes集群中的工作计算机。**每个节点都有一个Kubelet，它是一个管理节点并与Kubernetes主站通信的代理。该节点还应具有用于处理容器操作的工具，例如Docker或rkt。处理生产流量的Kubernetes集群应至少具有三个节点。在Kubernetes上部署应用程序时，您可以告诉主服务器启动应用程序容器。Master调度容器以在集群的节点上运行。**节点使用主服务器公开的Kubernetes API与Master进行通信 。**最终用户还可以直接使用Kubernetes API与集群进行交互。  

Kubernetes集群可以部署在物理机或虚拟机上。 要开始使用Kubernetes，您可以使用Minikube。 Minikube是一种轻量级Kubernetes实现，可在本地计算机上创建VM并部署仅包含一个节点的简单集群。 Minikube适用于Linux，macOS和Windows系统。 Minikube CLI提供了与群集一起使用的基本引导操作，包括启动，停止，状态和删除。 但是，对于本教程，您将使用预先安装了Minikube的在线终端。

## 使用kubectl创建部署

### 目标

- 了解应用程序部署。
- 使用kubectl在Kubernetes上部署您的第一个应用程序。

### Kubernetes部署

一旦运行了Kubernetes集群，就可以在其上部署容器化应用程序。为此，您需要创建Kubernetes Deployment配置。部署指示Kubernetes如何创建和更新应用程序实例。创建部署后，Kubernetes的Master计划将上述应用程序实例放到集群中的各个节点上。

创建应用程序实例后，Kubernetes Deployment Controller会持续监视这些实例。 如果托管实例的节点关闭或被删除，则Deployment控制器会将该实例替换为群集中另一个节点上的实例。 **这提供了一种自我修复机制来解决机器故障或维护问题**。

在预编排世界中，安装脚本通常用于启动应用程序，但它们不允许从机器故障中恢复。 通过创建应用程序实例并使它们在节点之间运行，Kubernetes Deployments提供了一种根本不同的应用程序管理方法。

##在Kubernetes上部署您的第一个应用程序

您可以使用Kubernetes命令行界面Kubectl创建和管理部署。 Kubectl使用Kubernetes API与集群进行交互。 在本单元中，您将学习创建在Kubernetes集群上运行应用程序的部署所需的最常见的Kubectl命令。

创建部署时，您需要指定应用程序的容器映像以及要运行的副本数。 您可以稍后通过更新部署来更改该信息; 训练营的模块5和6讨论了如何扩展和更新部署。

***应用程序需要打包成一种受支持的容器格式，以便部署在Kubernetes上***

## 查看pods和nodes
### 目标  

- 了解Kubernetes Pods。
- 了解Kubernetes Nodes。
- 排除部署的应用程序故障。

### Kubernetes Pods 
在模块2中创建部署时，Kubernetes创建了一个Pod来托管您的应用程序实例。 Pod是一个Kubernetes抽象，表示一组一个或多个应用程序容器（如Docker或rkt），以及这些容器的一些共享资源。 这些资源包括：

- 共享存储，作为volume
- 网络，作为唯一的集群IP地址
- 有关如何运行每个容器的信息，例如容器映像版本或要使用的特定端口

Pod为特定于应用程序的“逻辑主机”建模，并且可以包含相对紧密耦合的不同应用程序容器。 例如，Pod可能既包含带有Node.js应用程序的容器，也包含一个不同的容器，用于提供Node.js网络服务器要发布的数据。 Pod中的容器共享IP地址和端口空间，始终位于同一位置并共同调度，并在同一节点上的共享上下文中运行。

Pod是Kubernetes平台上的原子单元。 当我们在Kubernetes上创建部署时，该部署会在其中创建包含容器的Pod（而不是直接创建容器）。 每个Pod都与调度它的节点绑定，并保持在那里直到终止（根据重启策略）或删除。 如果节点发生故障，则会在群集中的其他可用节点上调度相同的Pod。

### Pods概述： 
![](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)

###节点 
Pod总是在节点上运行。 Node是Kubernetes中的工作计算机，可以是虚拟机或物理计算机，具体取决于集群。 每个节点由Master管理。 节点可以有多个pod，Kubernetes master会自动处理在群集中的节点上调度pod。 Master的自动调度考虑了每个节点上的可用资源。

每个Kubernetes节点至少运行：

- Kubelet，负责Kubernetes Master和Node之间通信的过程; 它管理Pod和机器上运行的容器。
- 容器运行时（如Docker，rkt）负责从注册表中提取容器映像，解压缩容器以及运行应用程序。

**节点概述**
![](https://d33wubrfki0l68.cloudfront.net/5cb72d407cbe2755e581b6de757e0d81760d5b86/a9df9/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg)





































































