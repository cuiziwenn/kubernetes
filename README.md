# kubernetes
## Kubernetes历史
* Kubernetes是Google10年容器化基础架构：borg，由于Docker的大规模运行，发现市场认可资源管理器，Google使用GO语言对Borg的系统编写开发出了Kubernetes
## Kubernetes特点
* 轻量级
* 开源
* 弹性
* 负载均衡

## Kubernetes架构

![imges](https://github.com/cuiziwenn/imgesfile/blob/master/K8S%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

* Kubernetes集群主要由Master和Node两类节点组成

* Master的组件包括：apiserver、controller-manager、scheduler和etcd等几个组件，其中apiserver是整个集群的网关。

* Node主要由kubelet、kube-proxy、docker引擎等组件组成。kubelet是K8S集群的工作与节点上的代理组件。

* 一个完整的K8S集群，还包括CoreDNS、Prometheus（或HeapSter）、Dashboard、Ingress Controller、Flannel等几个附加组件。其中cAdivsor组件作用于各个节点（master和node节点）之上，用于收集及收集容器及节点的CPU、内存以及磁盘资源的利用率指标数据，这些统计数据由Heapster聚合后，可以通过apiserver访问。

## Master组件
 
#### API Server
* K8S对外的唯一接口，提供HTTP/HTTPS RESTful API，即kubernetes API。所有的请求都需要经过这个接口进行通信。主要负责接收、校验并响应所有的REST请求，结果状态被持久存储在etcd当中，所有资源增删改查的唯一入口。

#### etcd
* 负责保存k8s 集群的配置信息和各种资源的状态信息，当数据发生变化时，etcd会快速地通知k8s相关组件。etcd是一个独立的服务组件，并不隶属于K8S集群。生产环境当中etcd应该以集群方式运行，以确保服务的可用性。  
* etcd不仅仅用于提供键值数据存储，而且还为其提供了监听（watch）机制，用于监听和推送变更。在K8S集群系统中，etcd的键值发生变化会通知倒API Server，并由其通过watch API向客户端输出。  
#### Controller Manager  
* 负责管理集群各种资源，保证资源处于预期的状态。Controller Manager由多种controller组成，包括replication controller、endpoints controller、namespace controller、serviceaccounts controller等 。由控制器完成的主要功能主要包括生命周期功能和API业务逻辑，具体如下：

* 生命周期功能：包括Namespace创建和生命周期、Event垃圾回收、Pod终止相关的垃圾回收、级联垃圾回收及Node垃圾回收等。

* API业务逻辑：例如，由ReplicaSet执行的Pod扩展等。
#### Schedule 调度器
* 资源调度，负责决定将Pod放到哪个Node上运行。Scheduler在调度时会对集群的结构进行分析，当前各个节点的负载，以及应用对高可用、性能等方面的需求。
## Node组件
Node主要负责提供容器的各种依赖环境，并接受Master管理。每个Node有以下几个组件构成
#### kubelet
* kubelet是node的agent，当Scheduler确定在某个Node上运行Pod后，会将Pod的具体配置信息（image、volume等）发送给该节点的kubelet，kubelet会根据这些信息创建和运行容器，并向master报告运行状态。
#### Docker Engine
* 每个Node都需要提供一个容器运行时（Container Runtime）环境，它负责下载镜像并运行容器，目前K8S支持的容器运行环境至少包括Docker、RKT、cri-o、Frak等
#### kube-proxy
* service在逻辑上代表了后端的多个Pod，外借通过service访问Pod。service接收到请求就需要kube-proxy完成转发到Pod，每个Node都会运行kube-proxy服务，负责将访问的service的TCP/UDP数据流转发到后端的容器，如果多个副本，kube-proxy会实现负载均衡，有2种方式：LVS或iptables
## 核心附件
#### kebeDNS
* 在K8S集群中调度并运行提供DNS服务的Pod，同一集群内的其他Pod可以使用该DNS服务来解决主机名。K8S自1.11版本开始默认使用CoreDNS项目来为集群提供服务注册和服务发现的动态名称解析服务
#### Dashboard
* K8S集群的全部功能都要基于Web的UI，来管理集群中的应用和集群自身。
#### Heapster
* 容器和节点的性能监控与分析系统，它收集并解析多种指标数据，如资源利用率、生命周期时间，在最新的版本当中，其主要功能逐渐由Prometheus结合其他的组件进行代替
#### Ingress Controller
* Service是一种工作于4层的负载均衡，而lngress是在应用层实现的HTTP（S）的负载均衡。不过，lngress资源自身并不能进行流量的穿透，它仅仅是一组路由规则的集合，这些规则需要通过lngress控制器（lngress Controller）发挥作用。目前该功能项目大概有：Nginx-ingress、Traefik、Envoy和HAproxy等。如下图就说Nginx-ingress的应用，具体地址链接：https://www.cnblogs.com/linuxk/p/9706720.html
#### Cluster
* Cluster是计算、存储和网络资源的集合，Kubernetes利用这些资源运行各种基于容器的应用。
#### Master
* Master是Cluster的大脑，它的主要职责是调度，即决定将应用放在哪里运行，Master运行Linux操作系统，可以是物理机或者虚拟机。为了实现高可用，可以运行多个Master
#### Node
* Node的职责是运行容器应用。Node由Master管理，Node负责监控并汇报容器的状态，并根据Master的要求管理容器的生命周期。Node运行在Linux操作系统，可以是物理机或者是虚拟机。
#### Pod
* Pod是Kubernetes是最小工作单元。每个Pod包含一个或多个容器。Pod中的容器会作为一个整体被Master调度到一个Node上运行。


