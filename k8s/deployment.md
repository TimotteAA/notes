基本的描述文件：

```yaml
# deployment的apiVersion
apiVersion: apps/v1
# 资源是deployment
kind: Deployment
# deployment的元数据
metadata:
  # deployment的名称
  name: myjob
  # namespace
  namespace: default
  # deployment的label
  labels:
    app: myjob
# 期望的状态
spec:
  # 通过selector匹配rs
  selector:
    matchLabels:
      app: myjob
  # replicaset
  replicas: 1
  strategy:
    # 更新策略
    rollingUpdate:
      # 在replicas的基础上，多0.25个
      maxSurge: 25%
      # 最多有0.25个更新失败
      maxUnavailable: 25%
    # 更新类型：滚动更新
    type: RollingUpdate
  # pod的规范
  template:
    metadata:
      labels:
        app: myjob
    spec:
      containers:
      - name: nginx-deployment
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
          limits:
            cpu: 100m
            memory: 100Mi
        env:
        - name: ACCEPT_EULA
          value: "Y"
        ports:
        - containerPort: 80
          name: http
          protocol: TCP
      restartPolicy: Always

```

运行后，查看资源：
![image-20240121140609155](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240121140609155.png)