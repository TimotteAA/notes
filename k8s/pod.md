一个pod的yaml描述：
```YAML
# apiVersion
apiVersion: v1
# 定义的k8s资源类型
kind: Pod
# 资源的元数据
metadata:
  # 名称
  name: "nginx"
  # 命名空间
  namespace: default
  # 标签，用于service、deployment的匹配
  labels:
    app: "MYAPP"
# pod的期望规格
spec:
  # pod里的容器
  containers:
  # 容器名称，随便取
  - name: test-nginx
    # 镜像名称
    image: "nginx:latest"
    imagePullPolicy: IfNotPresent # 如果本地不存在，则去远程repo拉
    # 对资源的一些配置
    resources:
      # 最大
      limits:
        # 1000m是一个核心
        cpu: 200m
        memory: 500Mi
      # 最低
      requests:
        cpu: 100m
        memory: 200Mi
    # 环境变量
    env:
    - name: DB_HOST
      value: "hahahahaha"
    # 定义容器的端口
    ports:
      # 容器暴露的端口
    - containerPort:  80
      # 名称
      name:  http
      # 端口用的协议类型
      protocol: TCP
  # pod的重启策略
  # OnFailure
  restartPolicy: OnFailure # 失败才重启
```

