早期的golang创建项目时必须在**$GOPATH**下，否则go项目是不能编译成功的，而后期随着代码革新，golang现在最常用的则是go module的方式来管理项目。

通常一个go项目下由一个或多个的package，go module就是一组统一发布的package的集合，通常在根目录下由**go.mod**文件，其中定义了依赖库的版本、**module path**，package以子文件夹的形式存在module中。



## go.mod

go module最重要的是go.mod文件的定义，它用来标记一个module和它的依赖库以及依赖库的版本。会放在module的主文件夹下，一般以`go.mod`命名。

一个go.mod内容类似下面的格式:

```go
module github.com/panicthis/modfile
go 1.16
require (
	github.com/cenk/backoff v2.2.1+incompatible
	github.com/coreos/bbolt v1.3.3
	github.com/edwingeng/doublejump v0.0.0-20200330080233-e4ea8bd1cbed
	github.com/stretchr/objx v0.3.0 // indirect
	github.com/stretchr/testify v1.7.0
	go.etcd.io/bbolt v1.3.6 // indirect
	go.etcd.io/etcd/client/v2 v2.305.0-rc.1
	go.etcd.io/etcd/client/v3 v3.5.0-rc.1
	golang.org/x/net v0.0.0-20210610132358-84b48f89b13b // indirect
	golang.org/x/sys v0.0.0-20210611083646-a4fc73990273 // indirect
)
exclude (
	go.etcd.io/etcd/client/v2 v2.305.0-rc.0
	go.etcd.io/etcd/client/v3 v3.5.0-rc.0
)
retract (
    v1.0.0 // 废弃的版本，请使用v1.1.0
)
```



## 语义化版本2.0

go.mod声明依赖库的版本时，采用的是**语义化版本规范2.0.0**，具体字段说明如下：
![image-20240825111758710](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240825111758710.png)

如果想要项目为自己的发版，可以把tag设置成上面的形式，比如`v1.3.0`、`v2.0.0-rc.1`等等。metadata中在Go版本比较时是不参与运算的，只是一个辅助信息。



#### module path

go.mod的第一行是module path, 一般采用仓库+module name的方式定义。这样我们获取一个module的时候，就可以到它的仓库中去查询，或者让go proxy到仓库中去查询。

```
module github.com/panicthis/modfile
```

如果你的版本已经大于等于2.0.0，按照Go的规范，你应该加上major的后缀，module path改成下面的方式:

```
module github.com/panicthis/modfile/v2
module github.com/panicthis/modfile/v3
```

而且引用代码的时候，也要加上`v2`、`v3`、`vx`后缀，以便和其它major版本进行区分。

这是一个很奇怪的约定，带来的好处是你一个项目中可以使用依赖库的不同的major版本，它们可以共存。



#### go directive

第二行是go directive，格式是 `go version`，指的是项目代码所需要的最低go版本。

这一行不是必须的，可以不写。



#### require

require段中列出了项目所需要的各个依赖库以及他们的版本，除了正规的 `v1.3.0`之外，还有一些奇怪的版本和注释：

**正式的版本号**

```go
github.com/coreos/bbolt v1.3.3
```



**伪版本号**

```go
github.com/edwingeng/doublejump v0.0.0-20200330080233-e4ea8bd1cbed
```

正式因为这个依赖库没有发布版本，而go module需要指定这个库的一个确定的版本，所以才创建的这样一个伪版本号。

go module的目的就是在go.mod中标记出这个项目所有的依赖以及它们确定的某个版本。

这里的`20200330080233`是这次提交的时间，格式是`yyyyMMddhhmmss`, 而`e4ea8bd1cbed`就是这个版本的commit id,通过这个字段，就可以确定这个库的特定的版本。

而前面的`v0.0.0`可能有多种生成方式，主要看你这个commit的base version:

- vX.0.0-yyyymmddhhmmss-abcdefabcdef: 如果没有base version,那么就是vX.0.0的形式
- vX.Y.Z-pre.0.yyyymmddhhmmss-abcdefabcdef： 如果base version是一个预发布的版本，比如vX.Y.Z-pre,那么它就用vX.Y.Z-pre.0的形式
- vX.Y.(Z+1)-0.yyyymmddhhmmss-abcdefabcdef: 如果base version是一个正式发布的版本，那么它就patch号加1，如vX.Y.(Z+1)-0



#### indirect注释

```go
go.etcd.io/bbolt v1.3.6 // indirect
golang.org/x/net v0.0.0-20210610132358-84b48f89b13b // indirect
golang.org/x/sys v0.0.0-20210611083646-a4fc73990273 // indirect
```

### 例子1：依赖丢失的情况

假设你有一个项目`myproject`，它依赖一个模块`A`，但是`A`的`go.mod`文件中遗漏了`B`这个依赖。

- 项目结构：

  ```
  go复制代码myproject/
  ├── go.mod
  └── main.go
  ```

- `main.go`:

  ```
  go复制代码package main
  
  import "A"
  
  func main() {
      A.SomeFunction()
  }
  ```

- `A`模块的`go.mod`文件:

  ```
  go复制代码module A
  
  require (
      // B 应该在这里，但被遗漏了
  )
  ```

在这种情况下，Go工具会自动在你的`myproject`的`go.mod`文件中添加`B`模块，并标记为`indirect`。

- myproject的go.mod

  ```
  module myproject
  
  require (
      A v1.0.0
      B v1.0.0 // indirect
  )
  ```

### 例子2：老旧模块没有`go.mod`

假设你的项目依赖一个很老的模块`C`，它并没有`go.mod`文件，并且它需要`D`作为依赖。

- 项目结构：

  ```
  myproject/
  ├── go.mod
  └── main.go
  ```

- `main.go`:

  ```go
  package main
  
  import "C"
  
  func main() {
      C.AnotherFunction()
  }
  ```

由于`C`没有`go.mod`文件，Go工具会在你的`myproject`的`go.mod`文件中自动添加`D`，并标记为`indirect`。

* myproject的go.mod

```go
module myproject

require (
    C v1.0.0
    D v1.0.0 // indirect
)
```

### 例子3：降级依赖

假设你在项目中使用了模块`E`，并且`E`的高版本依赖`F`，但是你决定将`E`降级到一个不再依赖`F`的版本。

- myproject的go.mod

  ```
  module myproject
  
  require (
      E v2.0.0
      F v1.0.0 // indirect
  )
  ```

你将`E`降级到`v1.0.0`版本，此时`F`不再是必需的依赖，但仍然会在`go.mod`中显示，并标记为`indirect`。

- 降级后的go.mod

  ```
  module myproject
  
  require (
      E v1.0.0
      F v1.0.0 // indirect
  )
  ```



#### incompatible

比如下面，有些库加了incompatibale：
```go
github.com/cenk/backoff v2.2.1+incompatible
```

这是因为这些项目虽然用了 `go.mod`，但是主版本已经大于2了，但是module path中并没有添加 v2、v3这样的后缀



#### exclude

如果你想在你的项目中跳过某个依赖库的某个版本，你就可以使用这个段。

```
exclude (
	go.etcd.io/etcd/client/v2 v2.305.0-rc.0
	go.etcd.io/etcd/client/v3 v3.5.0-rc.0
)
```

这样，Go在版本选择的时候，就会主动跳过这些版本，比如你使用`go get -u ......`或者`go get github.com/xxx/xxx@latest`等命令时，会执行version query的动作，这些版本不在考虑的范围之内。



#### replace

replace也是常用的一个手段，用来解决一些错误的依赖库的引用或者调试依赖库。

```
replace github.com/coreos/bbolt => go.etcd.io/bbolt v1.3.3
replace github.com/panicthis/A v1.1.0 => github.com/panicthis/R v1.8.0
replace github.com/coreos/bbolt => ../R
```

比如etcd v3.3.x的版本中错误的使用了`github.com/coreos/bbolt`作为bbolt的module path,其实这个库在它自己的go.mod中声明的module path是`go.etcd.io/bbolt`，又比如etcd使用的grpc版本有问题，你也可以通过replace替换成所需的grpc版本。

甚至你觉得某个依赖库有问题，自己fork到本地做修改，想调试一下，你也可以替换成本地的文件夹。

replace可以替换某个库的所有版本到另一个库的特定版本，也可以替换某个库的特定版本到另一个库的特定版本。



#### retact

retract是go 1.16中新增加的内容，借用学术界期刊撤稿的术语，宣布撤回库的某个版本。

如果你误发布了某个版本，或者事后发现某个版本不成熟，那么你可以推一个新的版本，在新的版本中，声明前面的某个版本被撤回，提示大家都不要用了。

撤回的版本tag依然还存在，go proxy也存在这个版本，所以你如果强制使用，还是可以使用的，否则这些版本就会被跳过。

和exclude的区别是retract是这个库的owner定义的， 而exclude是库的使用者在自己的go.mod中定义的。



# 常用命令

### 1. `go mod init`

- **功能**：初始化一个新的 Go 模块，并创建 `go.mod` 文件。

- 用法：在一个新的或现有的项目目录中使用，指定模块路径。

  ```
  go mod init example.com/mymodule
  ```

### 2. `go mod tidy`

- **功能**：清理并整理 `go.mod` 文件和 `go.sum` 文件。它会移除未使用的依赖，并下载缺少的依赖。

- 用法：在项目目录中运行，以确保依赖项的清晰和准确。

  ```
  go mod tidy
  ```

### 3. `go mod get`

- **功能**：获取指定版本的依赖包，并更新 `go.mod` 文件和 `go.sum` 文件。

- 用法：可以用于添加新依赖或升级/降级现有依赖。

  ```bash
  go mod get example.com/somepackage@v1.2.3
  ```

### 4. `go mod vendor`

- **功能**：将依赖项下载到 `vendor` 目录中。这个命令在需要将所有依赖放在项目本地时很有用，例如在一些构建环境中。

- 用法：

  ```bash
  go mod vendor
  ```

### 5. `go mod download`

- **功能**：下载 `go.mod` 文件中指定的所有模块到本地模块缓存中，但不会更新 `go.mod` 和 `go.sum` 文件。

- 用法：

  ```bash
  go mod download
  ```

### 6. `go mod verify`

- **功能**：验证 `go.sum` 文件中的模块是否在本地缓存中，并且未被篡改。这个命令确保依赖的完整性和安全性。

- 用法：

  ```bash
  go mod verify
  ```

### 7. `go mod graph`

- **功能**：显示模块的依赖关系图。这个命令有助于了解模块的依赖结构。

- 用法：

  ```bash
  go mod graph
  ```

### 8. `go mod edit`

- **功能**：手动编辑 `go.mod` 文件，添加、移除或更改依赖项。这个命令适合高级用户和自动化脚本。

- 用法：添加依赖、设置版本等。

  ```bash
  go mod edit -require=example.com/somepackage@v1.2.3
  ```

### 9. `go mod why`

- **功能**：解释为什么需要一个依赖。这个命令非常适合排查依赖项链条中的问题。

- 用法：

  ```bash
  go mod why example.com/somepackage
  ```



## 缓存仓库

在使用 Go Modules 的情况下，下载的依赖项不再存储在 `$GOPATH` 的 `src` 目录下，而是存储在 Go 模块的缓存目录中。具体来说，这些依赖项会被下载到 `$GOPATH/pkg/mod` 目录中。

### 详细说明：

1. **Go Modules 缓存目录**：

   - 依赖项会被下载到 `GOPATH/pkg/mod` 目录中，而不是 `$GOPATH/src`。
   - 这个目录是 Go Modules 的全局缓存目录，存储了所有项目使用的模块依赖。

   例如，如果你下载了一个依赖包 `example.com/somepackage@v1.0.0`，它会被放在类似于以下路径中：

   ```bash
   $GOPATH/pkg/mod/example.com/somepackage@v1.0.0/
   ```

2. **Go Modules 不依赖中央仓库**：

   - Go 没有一个像 Maven 中央仓库那样的中央仓库。相反，Go 使用分布式的方式，默认情况下，Go 从源代码托管平台（如 GitHub、GitLab 等）直接下载模块。

   - 如果没有特定配置，Go 会直接从模块的版本控制系统（如 Git）中拉取指定版本的代码。

   - 为了提高下载速度和可靠性，Go 语言提供了一个默认的模块代理 `proxy.golang.org`。这个代理会缓存常用模块，使得下载更快

     

#### 配置go proxy

因为神秘力量，通常需要配置go proxy：

如果你想使用一个自定义的代理，可以将 `GOPROXY` 设置为该代理的地址。

- **命令行方式（临时配置）**：

  ```
  bash
  复制代码
  export GOPROXY=https://goproxy.io
  ```

- **永久生效（适用于 Bash）**： 在 `~/.bashrc` 或 `~/.bash_profile` 中添加：

  ```
  bash
  复制代码
  export GOPROXY=https://goproxy.io
  ```

- **Windows 上设置**：

  ```
  cmd
  复制代码
  set GOPROXY=https://goproxy.io
  ```

#### b. **设置多个代理**

你可以为 `GOPROXY` 指定多个代理地址，以逗号分隔。Go 会依次尝试每个代理，直到找到所需的模块。

- **多代理配置**：

  ```
  bash
  复制代码
  export GOPROXY=https://goproxy.io,direct
  ```

  - `https://goproxy.io`：首先尝试从 `goproxy.io` 获取模块。
  - `direct`：如果 `goproxy.io` 无法提供所需模块，则直接从源码仓库获取。

