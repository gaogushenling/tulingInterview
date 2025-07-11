# 请简述Kubernetes的基本概念和核心组件

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Kubernetes（也称为k8s）是一个开源的容器编排系统，用于自动化应用程序容器的部署、扩展和管理。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Kubernetes的基本概念包括：</font>

1. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">节点（Node）</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：节点是运行应用程序容器的计算实例。每个节点都运行Kubelet和Docker引擎，并由Master节点进行管理和协调。</font>
2. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Master</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：Master是Kubernetes的控制节点，负责管理整个集群，并协调节点的工作。它由三个组件组成：API Server（负责API服务）、Controller Manager（负责容器编排）和Scheduler（负责容器调度）。</font>
3. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Pod</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：Pod是Kubernetes的基本单位，包含一个或多个相关的容器。这些容器在同一个Node上运行，共享相同的网络命名空间、IP地址和端口。</font>
4. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Service</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：Service是一个抽象层，定义了Pod的逻辑集合，并提供了访问它们的策略（如IP地址和端口）。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Kubernetes的核心组件包括：</font>

1. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">APIServer</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：负责API服务，处理所有集群级别的资源创建、调度和扩展等请求。</font>
2. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Controller Manager</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：负责管理集群的状态。例如，如果某个Node失效，Controller Manager会发现这个事实，并指导Kubelet重新启动在那上面运行的Pod。</font>
3. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Scheduler</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：负责在集群中找到适当的节点来运行Pod。它考虑了各种因素，如节点的处理能力、内存、磁盘容量等。</font>
4. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Kubelet</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：是Master节点在每个Node上的“眼线”。它定期向API Server汇报Node的状态，并接受Master的指令以采取适当的行动。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">这些组件共同协作，使得Kubernetes能够以自动化的方式管理、调度和运行容器化应用程序。</font>



> 更新: 2023-09-05 14:38:22  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/bn9wi3r3m9dpfeou>