service描述了集群内部的网路访问


 

![image-20240121143826136](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121143826136.png)

当创建好service后，k8s内部会自动创建一个endpoint，其中会记录着service的selector匹配的pod的ip地址、目标端口号。这样子pod就能通过endpoint去访问另一个pod。

详细步骤：

1. **创建 Service**：定义一个 Service，并为其指定一个选择器，该选择器匹配一组 Pod 的标签。
2. **选择器匹配 Pod**：Kubernetes 根据 Service 定义的选择器找到所有带有匹配标签的 Pod。
3. **自动创建 Endpoints**：Kubernetes 为每个匹配的 Pod 创建一个 Endpoint，并记录 Pod 的 IP 地址和端口号。Endpoints 资源的名称通常与 Service 的名称相同。
4. **服务发现**：当集群内的其他 Pod 需要与这些 Pod 通信时，它们会使用 Service 的名称。DNS 解析会自动解析为 Service 的虚拟 IP（Cluster IP），实际的网络流量将由 Service 根据 Endpoints 资源进行路由到具体的 Pod。
5. **负载均衡**：如果 Service 定义了多个 Pod，来自 Service 的请求将会在所有可用的 Endpoint 之间负载均衡。
6. **动态更新**：如果后续有新的 Pod 被创建或现有的 Pod 被删除，并且它们符合 Service 的选择器，Endpoints 资源会自动更新以反映这些变化。



yaml：
```YAML
# https://kubernetes.io/docs/concepts/services-networking/service/
apiVersion: v1
kind: Service
metadata:
  # 元数据
  name: myjob
  namespace: default
spec:
  # 匹配哪些pod，去代理请求
  # k,v匹配的方式
  selector:
    app: myjob
  # type支持：
  # NodePort，随机启动一个端口映射：30000->32727，映射到port，注意该端口直接绑定到host机上。
  # 
  type: ClusterIP
  # 端口映射
  ports:
  - name: myjob
    protocol: TCP
    # service的端口，集群内网的端口
    port: 80
    # 转发到目标pod的哪个端口
    targetPort: 5000
    nodePort: 30001


```



### 集群内布service访问集群外部服务

实现方式：

创建service时，不指定selector，手动创建endpoint

```bash
apiVersion: v1
kind: Endpoint
metadata:
  # 元数据
  name: myjob
  namespace: default
  labels: 
  	app: nginx
subnets:
- addresses:
	-ip: xxxx # service代理ip
	ports: # 和service一致
	- name: http
	  protocol: TCP
	  port: 80
```



### 基于域名的访问方式

Service也提供了基于域名的访问方式：

```YAML
apiVersion: v1
kind: Service
metadata:
  name: baidu-external-domain
  namespace: default
spec:
  type: ExternalName
  externalName: www.baidu.com
```

在集群内布的pod，可以直接通过 baidu-external-domain的方式去访问。