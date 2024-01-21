## k8s--容器自动化部署技术

### k8s干啥的

k8s提供了一种生产环境下的**自动化**容器编排：

![image-20240120193044769](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240120193044769.png)



用k8s部署一个服务的基本思路：

1. 编写Dockerfile文件，生成镜像
2. 利用k8s部署镜像，通过k8s的replica set，当服务挂掉后，k8s会自动重启一份pod节点

![image-20240120191142158](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240120191142158.png)



#### 有状态与无状态

![image-20240120193404994](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240120193404994.png)

对于nginx而言，无论增减机器，不同的机器上的nginx上做的是一样的。

对于redis，第二个机器上的token不知道第一个机器的token

从扩容的角度，redis需要数据迁移才能同步

**无状态应用**：不会存储数据到本地环境

**有状态应用**：服务对本地环境需要依赖（mysql、redis）

 

#### 资源和对象

k8s中，一切皆资源（用yaml描述），不同的资源，有不同的yaml对象来描述。

**换种理解，资源是类，对象是资源的实例**。

对象的创建、调度，都是通过api server的http restful接口来实现的。



##### 规约和状态

规约（spec）描述了对象的期望状态，以及一些基本对象。‘

状态（status），表示对象的实际状态，由k8s维护，通过控制器来对对象进行管理，让对象的实际状态尽可能地与期望状态靠拢。

##### 资源的分类

资源的类型可以分成：集群、命名空间、元空间

![image-20240121090601798](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121090601798.png)

对于资源的划分，主要是为了区分资源能够跨集群、空间的共享

**元数据**

1. hpa：根据机器情况下进行缩扩容：预定指标、自定义rest api情况

2. PodTemplate：创建pod的模板
3. LimitRange：对象所用cpu、内存的限制

**集群**

1. namespace命名空间
2. Node节点资源（服务器），k8s只是管理node上的资源
3. ClusterRole、ClusterRoleBinding：rbac鉴权用的

**命名空间级的资源**

1. pod：Pod 是 Kubernetes 中的基本工作单元，代表着在集群中运行的一个或多个容器的组合。

   每个 Pod 都有自己的 IP 地址，但这个地址在 Pod 重建时会改变。

   Pod 可以承载应用程序的实例，是部署容器化应用的基本单位。

   <img src="C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121125439306.png" alt="image-20240121125439306" style="zoom:150%;" />

​	pod中的pause容器，提供了容器的文件共享、网络共享的功能。

2. pod的控制器：在pod基础上添加了pod副本数的管理。

   1. 比如ReplicaSet，动态更新pod的副本数

      ![Image description](https://www.goglides.dev/images/GhRaUBQfkDNsSvvTtfYRyObUBm_JA_uNRM_Uhxoi9dY/w:880/mb:500000/ar:1/aHR0cHM6Ly93d3ct/Z29nbGlkZXMtZGV2/LnMzLmFtYXpvbmF3/cy5jb20vdXBsb2Fk/cy9hcnRpY2xlcy84/ajhtZm96dGU1NjN1/eHQ3cXFwYi5wbmc)

   2. deployment，在ReplicaSet上再封装，除了动态更新pod的副本数，还支持滚动升级与回滚（更新了描述pod的yaml，会创建新的rs）、暂停与恢复。deployment可以定义rs

​		![image-20240121130758147](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121130758147.png)

​	当你更新了一个 Deployment（比如，改变了容器的镜像版本）时，Kubernetes 会创建一个新的 ReplicaSet（ReplicaSet2）来开始部署新版本的 Pod。

​	新的 ReplicaSet 会逐渐增加新版本的 Pod 副本数量，同时旧的 ReplicaSet（ReplicaSet1）会逐渐减少旧版本的 Pod 副本数量。		

​	这个过程是滚动更新，意味着新旧版本的 Pod 会在一段时间内共存，以确保服务的可用性。

​	当新的 ReplicaSet 的 Pod 副本数达到了 Deployment 指定的期望副本数量，而旧的 ReplicaSet 的副本数降为零时，更新就完成了。





3. 适用于有状态服务：StatefulSet

4. 守护进程DaemonSet：基于selector，在选择的Node上部署一个容器副本，比如普罗米修斯的监控

   ![image-20240121095246527](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121095246527.png)



###### 服务发现

1. service命名空间的网络访问
2. ingress：集群外的网络访问

![image-20240121131631611](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121131631611.png)

###### 配置存储

1. ConfigMap，一个kv存储
2. Secret，相比ConfigMap多了加密的步骤





