# kubeEdge-OpenYurt
## KubeEdge 概述
![0.png](https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/0.png)

+ **KubeEdge**是一个开源系统，用于将容器化应用程序编排功能扩展到Edge的主机。它基于kubernetes构建，并为网络应用程序提供基础架构支持。云和边缘之间的部署和元数据同步。 KubeEdge使用**Apache 2.0**许可。并且绝对可以**免费**使用。KubeEdge v0.1于2018年12月底发布，具有非常基本的边缘功能。2020年6月5号发布了1.3.1版本，功能和架构上进行了很大程度的改进。
<br/>

+ **KubeEdge**由社区成员开发，他们是**Kubernetes/CNCF**的积极贡献者，并从事边缘计算研究。KubeEdge团队还积极与Kubernetes IOT/EDGE WORKING GROUP合作。在KubeEdge宣布的几个月内，它吸引了来自不同组织的成员，包括**京东、浙江大学、SEL实验室、Eclipse、中国移动、ARM、英特尔**共同构建平台和生态系统。
## 为什么会有KubeEdge
Kubernetes 作为容器编排的标准，自然会想把它应用到边缘计算上，即通过 kubernetes 在边缘侧部署应用，但是 kubernetes 在边缘侧部署应用时遇到了一些问题，例如：
+ 边缘侧设备没有足够的资源运行一个完整的 Kubelet
+ 一些边缘侧设备是 ARM 架构的，然而大部分的 Kubernetes 发行版并不支持 ARM 架构
+ 边缘侧网络很不稳定，甚至可能完全不通，而 kubernetes 需要实时通信，无法做到离线自治
+ 很多边缘设备都不支持TCP/IP 协议
+ Kubernetes 客户端（集群中的各个Node节点）是通过 list-watch 去监听 Master 节点的 apiserver 中资源的增删改查，list-watch 中的 watch 是调用资源的 watch API 监听资源变更事件，基于 HTTP 长连接实现，而维护一个 TCP 长连接开销较大。从而造成可扩展性受限。

Kubeedge试图解决：
+ **Kubernetes 原生支持**：使用 KubeEdge 用户可以在边缘节点上编排应用、管理设备并监控应用程序/设备状态，就如同在云端操作 Kubernetes 集群一样。
+ **云边可靠协作**：确保可靠的消息传递，不会在不稳定的云-边网络上丢失。
+ **边缘自治**：当云-边网络不稳定或边缘脱机并重新启动时，能确保边缘节点自主运行，并且边缘中的应用程序正常运行。
+ **边缘设备管理**：通过CRD实现的Kubernetes原生API管理边缘设备
+ **极轻量级边缘代理**：极轻量级边缘代理（EdgeCore），可在资源受限的边缘上运行。
+ **边缘计算**：通过在边缘端运行业务逻辑，可以在本地保护和处理大量数据。KubeEdge 减少了边和云之间的带宽请求，加快响应速度，并保护客户数据隐私。
## KubeEdge整体架构
![1.png](https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/1.png)

KubeEdge组件包含两部分：
+ 云上组件CloudCore
+ 边上组件EdgeCore

对于端设备，想要接入进KubeEdge里，则需要支持MQTT协议。用户可以自定义Mapper进行将端设备属性转化为MQTT消息，目前官方Mapper有：蓝牙和ModBus。
### In the Cloud
<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/2.png" width="500">

+ **CloudHub**：一个 Web Socket 服务端，负责监听云端的变化, 缓存并发送消息到 EdgeHub。
+ **EdgeController**：一个扩展的 Kubernetes 控制器，管理边缘节点和 Pods 的元数据确保数据能够传递到指定的边缘节点。
+ **DeviceController**：一个扩展的 Kubernetes 控制器，管理边缘设备，确保设备信息、设备状态的云边同步。
+ **CSI Driver**：同步存储数据到边缘。
+ **Admission Webhook**：为扩展API做合法性校验。
### On the Edge
<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/3.png" width="600">

+ **EdgeHub**：一个 Web Socket 客户端，负责与边缘计算的云服务（例如 KubeEdge 架构图中的 Edge Controller）交互，包括同步云端资源更新、报告边缘主机和设备状态变化到云端等功能。
+ **Edged**：运行在边缘节点的代理，用于管理容器化的应用程序。
+ **EventBus**：一个与 MQTT 服务器（mosquitto）交互的 MQTT 客户端，为其他组件提供订阅和发布功能。
+ **ServiceBus**：一个运行在边缘的HTTP客户端，接受来自云上服务的请求，与运行在边缘端的HTTP服务器交互，提供了云上服务通过HTTP协议访问边缘端HTTP服务器的能力。
+ **DeviceTwin**：负责存储设备状态并将设备状态同步到云，它还为应用程序提供查询接口。
+ **MetaManager**：消息处理器，位于 Edged 和 Edgehub 之间，它负责向轻量级数据库（SQLite）存储/检索元数据。
## KubeEdge发展路线图
+ 云边协同消息改为gRPC协议。 
+ 支持边缘集群EdgeSite纳入到KubeEdge。
+ 增强云边协同消息的可靠性和效率。
+ 使用edgemesh进行云边通信。
+ 支持Istio管理微服务。
+ 支持FAAS特性。
+ 边缘集群支持上千节点和百万端设备接入。
+ 大规模集群中能够智能调度。
+ 支持边缘节点的数据分析和查询。
+ 边端增加安全特性。
## OpenYurt 概述
<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/4.png" width="500">
<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/5.png" width="500">

使用 OpenYurt（Yurt，/jɜːrt/，蒙古包）作为开源项目名称，期望以其“形”来表示边缘计算侧重于创建一个集中管理但物理分布的基础设施，并支持自动/自治运行操作的含义。
<br/>

OpenYurt 主打“云边一体化”概念，依托原生 Kubernetes 强大的容器编排、调度能力，通过众多边缘计算应用场景锤炼，实现了一整套对原生 Kubernetes“零”侵入的边缘云原生方案，提供诸如边缘自治、高效运维通道、边缘单元化管理、边缘流量拓扑管理，安全容器、边缘 Serverless/FaaS、异构资源支持等能力。
<br/>

OpenYurt 能帮用户解决在海量边、端资源上完成大规模应用交付、运维、管控的问题，并提供中心服务下沉通道，实现和边缘计算应用的无缝对接。
## 为什么会有OpenYurt
期望解决在海量边、端资源上完成大规模应用交付、运维、管控的问题，并提供中心服务下沉通道，实现和边缘计算应用的无缝对接。
+ 使设备和工作负载之间的长距离网络流量最小化。
+ 克服网络带宽或可靠性限制。
+ 远程处理数据以减少延迟。
+ 提供更好的安全模型来处理敏感数据。

OpenYurt 技术方案有如下特点：
+ 对原生 Kubernetes“零”侵入，保证对原生 K8s API 的完全兼容。不改动 Kubernetes 核心组件，并不意味着 OpenYurt 是一个简单的 Kubernetes Addon。OpenYurt 通过 proxy node network traffic，对 Kubernetes 节点应用生命周期管理加了一层新的封装，提供边缘计算所需要的核心管控能力；
+ 无缝转换，OpenYurt 提供了工具将原生 Kubernetes“一键式”转换成支持边缘计算能力的 Kubernetes 集群；
+ 低 Overhead，OpenYurt 参考了大量边缘计算场景的实际需求，在保证功能和可靠性的基础上，本着最小化，最简化的设计理念，严格限制新增组件的资源诉求。
<br/>

OpenYurt 开源的核心能力包括：
+ **边缘自治能力**：YurtHub 作为节点上的临时配置中心，在网络连接中断的情况下，持续为节点上所有设备和客户业务提供数据配置服务。YurtHub 提供了对大量原生 Kubernetes API 的支持，可以在节点和边缘单元维度提供“Shadow Apiserver”的能力，在边缘计算弱网络链接场景的价值尤为突出；
+ **边缘运维通道**：在边缘场景，由于大多数边缘节点没有暴露在公网之上，中心管控无缝和边缘节点主动建立网络链接，所有的 Kubernetes 原生应用运维 APIs（logs/exec/metrics）会失去效力；YurtTunnel 通过在管控与边缘节点之间建立反向通道，并和节点的生命周期完整联动，承载原生运维 APIs 的流量；
+ **集群转换能力**：Yurtctl 作为 OpenYurt 官方命令行工具，提供原生 Kubernetes 集群支持边缘计算 infrastructure 的一键式切换。
## OpenYurt整体架构
![6.png](https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/6.png)

OpenYurt遵循经典的边缘应用程序架构设计：
集中式Kubernetes主服务器驻留在云站点中，该站点管理位于边缘站点的多个边缘节点。 

每个边缘节点具有适度的计算资源，从而允许运行大量边缘应用程序以及Kubernetes节点守护程序。

集群中的边缘节点可以跨越多个物理区域。

Unit在OpenYurt中可以互换。

<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/7.png" width="500">

YurtHub是一个节点守护程序，它充当从Kubernetes节点到主节点的出站流量的代理。 中继出站流量时，它将缓存所有状态并将其持久保存在本地。 如果与数据中心的连接丢失或Kubelet重新启动，则Kubelet将从YurtHub的缓存中读取。

<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/8.png" width="500">

OpenYurt提供了一个控制器管理器，用于管理一些控制器，例如节点控制器和单元控制器，以用于不同的边缘计算用例。 功能之一是，即使缺少节点心跳，处于自治模式的节点中的Pod也不会从API Server中撤出。
## KubeEdge发展路线图
![9.png](https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/9.png)

作为阿里云容器服务 ACK@Edge 的开源版本，OpenYurt 将采用全开源社区开发模式
每季度发布新版本更新，包含社区上游安全/关键 bug 修复和新特性、新能力。
逐步将产品完整能力开源，预计到 2021 年一季度正式发布 OpenYurt 1.0 版本

现状：
+ **边缘自治能力**(YurtHub: 已开源)，保证在弱网或者重启节点的情况下，部署在边缘节点上的应用也能正常运行；
+ **云边协同能力**(待开源)，通过云边运维通道解决边缘的运维需求，同时提供云边协同能力；
+ **单元化管理能力**(待开源)，为分散的边缘节点，边缘应用，应用间流量提供单元化闭环管理能力；
## KubeEdge vs OpenYurt
![10.png](https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/10.png)

## Tips
+ 从架构上讲各有优点：OpenYurt简洁，KubeEdge成熟；
+ 现阶段如果做产品选择KubeEdge：1 项目成熟；2 设备管理；3 发展路线清晰；4 开源；
+ OpenYurt未来的走向需要关注；
+ KubeEdge是个边缘平台，离可以实现POC还需要做一些工作；
+ gu
## Reference Link
+ https://github.com/kubeedge/
+ https://kubeedge.io/zh/
+ https://docs.kubeedge.io/en/latest/
+ https://github.com/alibaba/openyurt/
+ https://github.com/alibaba/openyurt/blob/master/docs/tutorial/yurtctl.md
+ http://www.kchuhai.com/report/view-6092.html
+ https://msd.misuland.com/pd/4146263948980652671
+ https://dzone.com/articles/introducing-main-feature-of-openyurt-local-autonom
+ https://www.cnblogs.com/wsjhk/p/12103998.html
## Ref. KubeEdge Mapper
![11.png](https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/11.png)

**Mapper**用来连接和控制边缘设备，将边缘设备映射到Device实例，同时通过Device实例的属性控制边缘设备。

目前Mapper支持两种协议：
+ **BlueTooth**
+ **Modbus**

如果边缘设备不是通过蓝牙和Modbus协议接入，则需要用户自行实现Mapper。

Mapper进程通过设备Device实例在Kubernetes中保存的ConfigMap读取设备信息。
## Ref. OpenYurt Yurtctl
<img src="https://github.com/inspursoft/kubeEdge-OpenYurt/blob/master/img/12.png" width="500">

一键让原生 K8s 集群具备边缘计算能力 
```
    yurtctl convert --provider [minikube|ack]
```
为了让原生 K8s 集群具备边缘计算能力，OpenYurt 以 addon 为载体，非侵入式增强原生 K8s。

+ Yurtctl 是一个**中心化**的管控工具。在 OpenYurt 云-边一体的架构里，Yurtctl 将直接与 APIServer 进行交互。它借助原生 Kubernetes 的 Job workload 对每个 node 进行运维操作。如图1所示，在执行转换（convert）操作时，Yurtctl 会通过 Job 将一个 servant Pod 部署到用户指定的边缘节点上，servant Pod 里的容器执行的具体操作请参考：https://github.com/alibaba/openyurt/blob/master/config/yurtctl-servant/setup_edgenode。

+ 由于 servant Pod 需要直接操作节点 root 用户的文件系统（例如将 yurthub 配置文件放置于 /etc/kubernetes/manifests 目录下），并且需要重置系统管理程序（kubelet.service）， servant Pod 中的 container 将被赋予 privileged 权限，允许其与节点共享 pid namespace，并将借由 nsenter 命令进入节点主命名空间完成相关操作。当 servant Job 成功执行后，Job 会自动删除。如果失败，Job 则会被保留，方便运维人员排查错误原因。借由该机制，Yurtctl 还可对 Yurthub 进行更新或者删除。