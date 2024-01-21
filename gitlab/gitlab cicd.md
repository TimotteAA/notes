## ci/cd

早起传统的软件开发流程：

* 开发团队：在开发环境中完成开发，单元测试通过后，提交到仓库
* 运维人员：把开发的代码打包，上传到测试环境上去
* 测试：开测，测完通过；运维再上传到生成环境中，在进行测试

![image-20240114082240271](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114082240271.png)



#### 可能存在的问题：

**人工低级错误发生** 产品和服务交付中的关键活动全都需要手动操作。

**团队工作效率低** 需要等待他人的工作完成后才能进行自己的工作。

**开发运维对立** 开发人员想要快速更新，运维人员追求稳定，各自的针对的方向不同。



### ci：持续集成，cd：持续交付/持续部署

### 持续集成 （CI）

持续合并开发人员正在开发编写的所有代码的一种做法。通常一天内进行多次合并和提交代码，从存储库或生产环境中进行构建和自动化测试，以确保没有集成问题并及早发现任何问题。

**开发人员提交代码的时候一般先在本地测试验证，只要开发人员提交代码到版本控制系统就会触发一条提交流水线，对本次提交进行验证。**



对于cd，一般有两种解释：

### 持续交付 （CD）

[持续交付](https://continuousdelivery.com/)是超越持续集成的一步。不仅会在推送到代码库的每次代码更改时都进行构建和测试，而且，作为附加步骤，即使部署是手动触发的，它也可以连续部署。此方法可确保自动检查代码，但需要人工干预才能从策略上手动触发更改的部署。

### 持续部署 (CD)

通常可以通过将更改自动推送到发布系统来随时将软件发布到生产环境中。持续部署 会更进一步，并自动将更改推送到生产中。类似于持续交付，[持续部署](https://www.airpair.com/continuous-deployment/posts/continuous-deployment-for-practical-people)也是超越持续集成的又一步。不同之处在于，您无需将其手动部署，而是将其设置为自动部署。部署您的应用程序完全不需要人工干预。



## gitlab cicd

gitlab cicd架构可以由下图说明：

![image-20240114082726630](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114082726630.png)

1. 一个gitlab server，包含了代码仓库
2.  runner server，在开发人员提交到代码仓库后，根据 .gitlab-ci.yml 去匹配runner干活
3. 代码仓库中的 .gitlab-ci.yml 文件，指定了整套流水线的一个个stage、干活的runner



```bash
# 安装gitlab社区版需要的组件
sudo dnf install -y curl policycoreutils openssh-server perl

# 添加gitlab repo
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

# 安装gitlab
sudo EXTERNAL_URL="http://yourdomain.com" dnf install -y gitlab-ce

## 等待一坨时间
cd /ect/gitlab
cat initial_root_password # 这里包含了初始密码
```



#### gitlab runner

类型：

1. project，项目类型，只针对单独的project
2. group，一个group包含多个project，所有的项目都会运行
3. specific 项目类型，针对特定的项目作业

状态

1. locked：锁定状态，无法运行项目作业
2. paused：暂停状态



首先是shared runner：

![image-20240114104153572](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114104153572.png)

然后创建一个group，创建项目，可以看到一个project runner：

![image-20240114105018370](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114105018370.png)



安装gitlab runner，这里采用docker的方式：

```BASH
docker pull gitlab/gitlab-runner

docker run -d --name gitlab-runner --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /etc/gitlab-runner \
  gitlab/gitlab-runner:latest

# gitlab runner的配置卷匿名即可

# 注册runner
docker exec -it gitlab-runner gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=48 revision=102c81ba version=16.7.0
Running in system-mode.                            
                                                   
Enter the GitLab instance URL (for example, https://gitlab.com/):
# 输入gitlab url
https://xxx

Enter the registration token:
# gitlab里可以拿到
Enter a description for the runner:

[80e6ad02bfaa]: first shared runner


Enter tags for the runner (comma-separated):
tag 后面有用
deploy,build
Enter optional maintenance note for the runner:

WARNING: Support for registration tokens and runner parameters in the 'register' command has been deprecated in GitLab Runner 15.6 and will be replaced with support for authentication tokens. For more information, see https://docs.gitlab.com/ee/ci/runners/new_creation_workflow 
Registering runner... succeeded                     runner=FnNtNDRG

# runner执行器的类型，常用custom、docker、kubetnetes
Enter an executor: custom, docker-windows, parallels, virtualbox, docker-autoscaler, docker+machine, instance, kubernetes, docker, shell, ssh:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
 
Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml" 



```

结果：
![image-20240114114919985](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114114919985.png)



在项目根目录中，添加下面的.gitlab-ci.yml文件：

```yml
stages:
  - build
  - deploy
 

build:
  stage: build
  tags:
    - build
  only:
    - master
  script:
    - echo "npm install"


deploy:
  stage: deploy
  tags:
    - deploy
  only:
    - master
  script:
    - echo "hello deploy"
```



结果：

![image-20240114115915315](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114115915315.png)



#### gitlab ci语法

##### job（产物是共享滴）

每个ci文件都可以定义一个job，每个job都必须有script

```bash
job1:
  script: "execute-script-for-job1"

job2:
  script: "execute-script-for-job2"
```



- 可以定义一个或多个作业(job)。
- 每个作业必须具有唯一的名称（不能使用关键字）。
- 每个作业是独立执行的。
- 每个作业至少要包含一个script。

##### script

scrpt是个数组，定义一个个地linux脚本：

```bah
ob:
  script:
    - uname -a
    - bundle exec rspec
```

##### before_script

##### after_script

如其名，在script执行前、后执行的script，**必须是个数组**

可以定义在全局，也可以定义在job中，job中会覆盖全局

```bash
stages:
  # - build
  - deploy

# 全局的before_script
before_script:
  - ehco "before-script!"

test:
  stage:
    deploy
  tags:
    - deploy
  only:
    - master
  before_script: 
    - echo "test before-script"
  script:
    - echo "test script"
  after_script:
    - echo "test after_script"
```



after_script失败不会影响作业失败，而before_script会：

![image-20240114122723502](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114122723502.png)



![image-20240114122731048](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114122731048.png)



##### stages

用于定义作业可以使用的阶段，并且是全局定义的。同一阶段的作业并行运行，不同阶段按顺序执行。

```yml
stages：
  - build
  - test
  - deploy
```



这里定义了三个阶段，首先build阶段并行运行，然后test阶段并行运行，最后deploy阶段并行运行。deploy阶段运行成功后将提交状态标记为passed状态。如果任何一个阶段运行失败，最后提交状态为failed。



##### .pre和.post

.pre是整个流水线的第一个阶段，.post是整个流水线的最后一个运行阶段。

自定义的stage在这两者之间运行



##### stage

在job中可以定义stage，依赖于全局定义的stages，同一个stage的不同runner是并发执行：
```YML
unittest:
  stage: test
  script:
    - echo "run test"
    
interfacetest:
  stage: test
  script:
    - echo "run test"
```

可能遇到的问题：job没有同时运行

两个job用了同一个runner，而runner的并发数没修改：

vim /etc/gitlab-runner/config.toml:

```bash
concurrent = 10
```



##### variables

定义变量，pipeline变量、job变量、Runner变量。job变量优先级最大。

综合实例：
```bash
before_script:
  - echo "before-script!!"

variables:
  DOMAIN: example.com
  
stages:
  - build
  - test
  - codescan
  - deploy

build:
  before_script:
    - echo "before-script in job"
  stage: build
  script:
    - echo "mvn clean "
    - echo "mvn install"
    - echo "$DOMAIN"
  after_script:
    - echo "after script in buildjob"

unittest:
  stage: test
  script:
    - echo "run test"

deploy:
  stage: deploy
  script:
    - echo "hello deploy"
    - sleep 2;
  
codescan:
  stage: codescan
  script:
    - echo "codescan"
    - sleep 5;
 
after_script:
  - echo "after-script"
  - ech
```



## ci文件语法二

##### tags

在创建、编辑runner时，可以设定runner的tag。在job中，也可以设定tags，用来指定对应的runner来执行job：

```yaml
job2:
	tags:
		- build
		- test
		
	script:
		- echo "test"
```

![image-20240114130859852](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114130859852.png)

##### allow_failure

allow_failure默认值为false，表示不允许script的失败。如果启用后，在cicd流水线的stage将会显示橙色警告，但是整个流水线并不会失败。

假设所有其他作业均成功，则该作业的阶段及其管道将显示相同的橙色警告。但是，关联的提交将被标记为"通过”，而不会发出警告。

```yaml
jobx:
	tags:
		- test
		- build
    script:
    	- ech "a"
    allow_failure: true
```

##### when

when用于job的执行时机，默认是on_success，即前面的是所有job都成功，才会执行本job

1. on_success：前面的job都必须成功
2. on_failure：前面的阶段出现失败
3. always：不论前面啥结果，都执行
4. manual：在cicd面板上，点击后才会执行
5. delayed：延时执行，starts_in指定延时时间

![image-20240114131227052](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240114131227052.png)

##### delayed

延时一段时间后执行，有效值

**‘5'，10 seconds，30minutes，1day，1week**



#### 实验性demo

```yaml
before_script:
  - echo "before-script!!"

variables:
  DOMAIN: example.com
  
stages:
  - build
  - test
  - codescan
  - deploy

# 这个job不会失败
build:
  before_script:
    - echo "before-script in job"
  stage: build
  script:
    - echo "mvn clean "
    - echo "mvn install"
    - echo "$DOMAIN"
  after_script:
    - echo "after script in buildjob"

# 延时3s后失败
unittest:
  stage: test
  script:
    - ech "run test"
  when: delayed
  start_in: '3'
  allow_failure: true
  
# 手动执行
deploy:
  stage: deploy
  script:
    - echo "hello deploy"
    - sleep 2;
  when: manual
  allow_failure: true

# 这个job不会执行
codescan:
  stage: codescan
  script:
    - echo "codescan"
    - sleep 5;
  when: on_success
 
after_script:
  - echo "after-script"
  - ech
  

```

##### retry

retry配置job失败情况下重试job的次数

当作业失败并配置了`retry` ，将再次处理该作业，直到达到`retry`关键字指定的次数。如果`retry`设置为2，并且作业在第二次运行成功（第一次重试），则不会再次重试. `retry`值必须是一个正整数，等于或大于0，但小于或等于2（最多两次重试，总共运行3次）.

```bash
unittest:
  stage: test
  retry: 2
  script:
    - ech "run test"
```

进一步的，可以指定retry的原因，比如script_failure

```yaml
unittest:
	stage: test
	tags:
		- test
	script:
		- ech
    retry:
    	max: 2
    	when:
    		- script_failure
```

```bash
always ：在发生任何故障时重试（默认）.
unknown_failure ：当失败原因未知时。
script_failure ：脚本失败时重试。
api_failure ：API失败重试。
stuck_or_timeout_failure ：作业卡住或超时时。
runner_system_failure ：运行系统发生故障。
missing_dependency_failure: 如果依赖丢失。
runner_unsupported ：Runner不受支持。
stale_schedule ：无法执行延迟的作业。
job_execution_timeout ：脚本超出了为作业设置的最大执行时间。
archived_failure ：作业已存档且无法运行。
unmet_prerequisites ：作业未能完成先决条件任务。
scheduler_failure ：调度程序未能将作业分配给运行scheduler_failure。
data_integrity_failure ：检测到结构完整性问题。
```

##### timeout超时

在job中，可以指定超时时间。同时还可以指定项目的超时时间，但是都不会超过runner的超时时间

```YAML
build:
  script: build.sh
  timeout: 3 hours 30 minutes

test:
  script: rspec
  timeout: 3h 30m
```



**示例1-运行程序超时大于项目超时**

runner超时设置为24小时，项目的CI / CD超时设置为2小时。该工作将在2小时后超时。

**示例2-未配置运行程序超时**

runner不设置超时时间，项目的CI / CD超时设置为2小时。该工作将在2小时后超时。

**示例3-运行程序超时小于项目超时**

runner超时设置为30分钟，项目的CI / CD超时设置为2小时。工作在30分钟后将超时



##### parallel

parallel指定job的并发运行实例数，>=2，<=10

```yaml
unittest:
	statge: test
	tags:
		- test
    only:
    	master
    parallel: 5
    script:
    	- echo "hahahahahaha"
```



#### 综合实例

```yaml
before_script:
  - echo "before-script!!"

variables:
  DOMAIN: example.com
  
stages:
  - build
  - test
  - codescan
  - deploy

build:
  before_script:
    - echo "before-script in job"
  stage: build
  script:
    - echo "mvn clean "
    - echo "mvn install"
    - echo "$DOMAIN"
  after_script:
    - echo "after script in buildjob"

unittest:
  stage: test
  script:
    - ech "run test"
  when: delayed
  start_in: '5'
  allow_failure: true
  # 脚本出错retry，最多2次
  retry:
    max: 1
    when:
      - script_failure
  timeout: 1 hours 10 minutes
  
  
# 手动执行的job
deploy:
  stage: deploy
  script:
    - echo "hello deploy"
    - sleep 2;
  when: manual

# 不会成功
codescan:
  stage: codescan
  script:
    - echo "codescan"
    - sleep 5;
  when: on_success
  parallel: 5
 
after_script:
  - echo "after-script"
  - ech
```

## 语法3

##### only except

only用于限定job对应的分支，except则是该分支不执行该job

示例：

```yaml
test:
	stage: test
	tags:
		- test
    script:
    	- echo "hahahaha"
    only:
    	- master # master分支才执行test job

test2:
	stage: test2
	tags:
		- test2
    script:
    	- echo "aaaa"
    except:
    	- develop # develop分支不执行test2 job
```



##### rules关键词

rules关键词更精细，可以条件式地让job具有某些特性，rules是个数组，按顺序匹配，匹配上就结束

if: 条件

```yaml
variables:
  DOMAIN: example.com

codescan:
  stage: codescan
  tags:
    - build
  script:
    - echo "codescan"
    - sleep 5;
  #parallel: 5
  rules:
  	  # 如果domain是example.com，则这个job是手动的
    - if: '$DOMAIN == "example.com"'
      when: manual
      # 如果domain不是example.com，则这个job在前面全部成功才会执行
    - when: on_success
```

rules:changes

```yaml
codescan:
  stage: codescan
  tags:
    - build
  script:
    - echo "codescan"
    - sleep 5;
  #parallel: 5
  rules:
  	#dockerfile变化也执行这个job
    - changes:
      - Dockerfile
      when: manual
    - if: '$DOMAIN == "example.com"'
      when: on_success
    - when: on_success
```

rules:exists

接受文件路径数组。当仓库中存在指定的文件时操作。

```bash
codescan:
  stage: codescan
  tags:
    - build
  script:
    - echo "codescan"
    - sleep 5;
  #parallel: 5
  rules:
    - exists:
      - Dockerfile
      when: manual 
    - changes:
      - Dockerfile
      when: on_success
    - if: '$DOMAIN == "example.com"'
      when: on_success
    - when: on_success
```

rules:allow_failure

```yaml
job:
  script: "echo Hello, Rules!"
  rules:
    - if: '$CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "master"'
      when: manual
      allow_failure: true
      # if成立后，编程手动的job，且允许失败？

```

workflow，控制整个流水线是否执行

```yaml
variables:
  DOMAIN: example.com

# 指定域名下，才执行整个流水线
workflow:
  rules:
    - if: '$DOMAIN == "example.com"'
    - when: always
```

#### 综合实例：

```yaml
before_script:
  - echo "before-script!!"

variables:
  DOMAIN: example.com
  
workflow:
  rules:
    - if: '$DOMAIN == "example.com"'
      when: always
    - when: never
    
stages:
  - build
  - test
  - codescan
  - deploy

build:
  before_script:
    - echo "before-script in job"
  stage: build
  script:
    - echo "mvn clean "
    - echo "mvn install"
    - ech "$DOMAIN"
  after_script:
    - echo "after script in buildjob"
  rules:
    - exists:
      - Dockerfile
      when: on_success 
      allow_failure: true

    - changes:
      - Dockerfile
      when: manual
    - when: on_failure

unittest:
  stage: test
  script:
    - ech "run test"
  when: delayed
  start_in: '5'
  allow_failure: true
  retry:
    max: 1
    when:
      - script_failure
  timeout: 1 hours 10 minutes
  
  

deploy:
  stage: deploy
  script:
    - echo "hello deploy"
    - sleep 2;
  rules:
    - if: '$DOMAIN == "example.com"'
      when: manual
    - if: '$DOMAIN == "aexample.com"'
      when: delayed
      start_in: '5'
    - when: on_failure
  
codescan:
  stage: codescan
  script:
    - echo "codescan"
    - sleep 5;
  when: on_success
  parallel: 5
 
after_script:
  - echo "after-script"
  - ech
```

##### cache

在job可以定义缓存目录进行复用，缓存在gitlab-runner的服务里，打包完成上传到里面，要用时在下下载下来。

cache.paths，指定当前目录下要缓存的目录

```yaml
# 全局缓存
cache:
	key: build
	paths:
		- xxxx

build:
	stage: build
	tags:
		- build
    # job的cache覆盖全局cache
    cache:
    	key: build
    	paths:
    		- node_modules
    
```

##### cache.key

为缓存做个标记，可以配置job、分支为key来实现分支、作业特定的缓存。为不同 job 定义了不同的 `cache:key` 时， 会为每个 job 分配一个独立的 cache。cache:key`变量可以使用任何预定义变量，默认`default ，从GitLab 9.0开始，默认情况下所有内容都在管道和作业之间共享。



files：文件变化后重新生成缓存（最多两个）
```yaml
cache:
  key:
    files:
      - package.json
  paths:
    - node_modules
```



##### cache.policy 策略

默认：在执行开始时下载文件，并在结束时重新上传文件。称为” `pull-push`缓存策略.

`policy: pull` ，只读，只会下载，但不会更新

`policy: push` ，不管三七二十一，都更新缓存

#### 综合案例

```yaml
before_script:
  - echo "before-script!!"

variables:
  DOMAIN: example.com

# 缓存target目录
cache: 
  paths:
   - target/

stages:
  - build
  - test
  - deploy
  
build:
  before_script:
    - echo "before-script in job"
  stage: build
  tags:
    - build
  only:
    - master
  script:
    - ls
    - id
    - mvn clean package -DskipTests
    - ls target
    - echo "$DOMAIN"
    - false && true ; exit_code=$?
    - if [ $exit_code -ne 0 ]; then echo "Previous command failed"; fi;
    - sleep 2;
  after_script:
    - echo "after script in job"


unittest:
  stage: test
  tags:
    - build
  only:
    - master
  script:
    - echo "run test"
    - echo 'test' >> target/a.txt
    - ls target
  retry:
    max: 2
    when:
      - script_failure
  
deploy:
  stage: deploy
  tags:
    - build
  only:
    - master
  script:
    - echo "run deploy"
    - ls target
  retry:
    max: 2
    when:
      - script_failure
      

after_script:
  - echo "after-script"
```



配合策略：
```yaml
job:
  cache:
     paths:
         - target/ #与pipeline中的路径 相同
     policy: push #仅上传，不下载，之前的缓存都不会被使用
```



## cicd语法5

##### artifacts-制品

1. - 在 GitLab CI/CD 中，`artifacts` 是指在 CI/CD 流程中由特定作业生成的文件或目录。
   - 它们通常用于存储构建结果、测试报告、日志文件等，以便于在后续的作业中使用或作为构建的最终产物。
2. **Artifacts 的可访问性**：
   - 一旦某个作业产生了 `artifacts`，这些文件会上传到 GitLab 服务器，并且可以在 GitLab 的 UI 中进行下载。
   - 这使得团队成员能够轻松地访问构建结果，无论他们是否有访问运行该作业的实际运行环境的能力。
3. **Artifacts 的配置**：
   - 在 `.gitlab-ci.yml` 配置文件中，可以使用 `artifacts` 关键字来指定哪些文件或目录应当被视为制品，并且上传到 GitLab 服务器。
   - 可以指定 `artifacts` 的有效期限，超过这个期限，制品将被自动删除。



基本示例：
```YAML
test:
  stage: test
  script:
    - npm install
    - npm run test # 假设这个命令运行 Jest
  artifacts:
  	# 路径是相对于项目目录的，不能直接链接到项目目录之外，下面的文件都是制品
    paths:
      - coverage/ # 假设 Jest 将覆盖率报告输出到这个目录
      - test-results.xml # 假设这是 Jest 生成的测试结果文件
    expose_as: 'hahahaha' # 把两个制品在ui合称为hahahaha
    # 通过name指令定义所创建的工件存档的名称。可以为每个档案使用唯一的名称。
    name: "$CI_JOB_NAME"
    expire_in: 1 week # 设置 artifacts 的过期时间
    when: on_failure # 在失败时上传制品
    # when的其他值：on_success、always
```



##### dependencies，获取前面的制品

```yaml
test:
  stage: test
  script:
    - npm install
    - npm run test # 假设这个命令运行 Jest
  artifacts:
  	# 路径是相对于项目目录的，不能直接链接到项目目录之外，下面的文件都是制品
    paths:
      - coverage/ # 假设 Jest 将覆盖率报告输出到这个目录
      - test-results.xml # 假设这是 Jest 生成的测试结果文件
    expose_as: 'hahahaha' # 把两个制品在ui合称为hahahaha
    # 通过name指令定义所创建的工件存档的名称。可以为每个档案使用唯一的名称。
    name: "$CI_JOB_NAME"
    expire_in: 1 week # 设置 artifacts 的过期时间
    when: on_failure # 在失败时上传制品
    # when的其他值：on_success、always
    
build:
	stage: build
	# 拿test这个job的制品
	dependencies:
      - test
```

## cicd6

##### needs，并行job

直接看示例：

```YAML
stages:
  - build
  - test
  - deploy

module-a-build:
  stage: build
  script: 
    - echo "hello3a"
    - sleep 10
    
module-b-build:
  stage: build
  script: 
    - echo "hello3b"
    - sleep 20
    
## 没有needs，上面两个都完成后，才能进入下一个stage
# 有了needs后，当build中的 module-a-build完成后
# test步骤里的module-a-test可以立刻执行，而不用等待module-b-build的job完成


module-a-test:
  stage: test
  script: 
    - echo "hello3a"
    - sleep 10
  needs: 
  	- job: 'module-a-build'
  	  artifacts: true # 下载module-a-build的制品
    
module-b-test:
  stage: test
  script: 
    - echo "hello3b"
    - sleep 10
  needs: ["module-b-build"]
    
```

##### include，引入外部yaml文件

include可以引入本仓库yml文件、远程仓库（http或者https）的yml文件

定义一个local：

```yml
stages:
  - deploy
  
deployjob:
  stage: deploy
  script:
    - echo 'deploy'
```

gitlab-ci.yml引入：
```yml
include:
  local: 'ci/localci.yml'
  

stages:
  - build
  - test
  - deploy
  

buildjob:
  stage: build
  script: ls
  
 
testjob:
  stage: test
  script: ls
```

2、引入另一项目的yml文件：

```YAML
include:
  - project: GROUP/PROJECT
    ref: master
    file: '.gitlab-ci.yml'
```

3、引入远程仓库的yml文件，只能是get请求，不能有身份校验：

```yml
include:
  - remote: 'https://gitlab.com/awesome-project/raw/master/.gitlab-ci-template.yml'
```

##### extends 定义模板继承

"."dot开头的job，默认是不会被运行的，因此可以定义好模板，然后继承使用：
```yml
.test:
  stage: test
  tags:
  	-test
  script:
  	- 'echo ,test template'
  	
test:
  extends: .test
  script:
  	- 'ehco test job'
	only:
	- dev
```

继承后会进行合并

##### trigger，触发下游管道

当GitLab从`trigger`定义创建的作业启动时，将创建一个下游管道。允许创建多项目管道和子管道。将`trigger`与`when:manual`一起使用会导致错误。

多项目管道： **跨多个项目**设置流水线，以便一个项目中的管道可以触发另一个项目中的管道。[微服务架构]

父子管道: **在同一项目中**管道可以触发一组同时运行的子管道,子管道仍然按照阶段顺序执行其每个作业，但是可以自由地继续执行各个阶段，而不必等待父管道中无关的作业完成。



###### 多项目管道

当project1成功后，触发project2 master流水线：

```yaml
staging:
  variables:
    ENVIRONMENT: staging
  stage: deploy
  trigger: 
    project: demo/demo-java-service
    branch: master
    strategy: depend
```

**project**关键词，用于指定下游项目的完整路径（group/project)，branch指定分支名称。

默认情况下，一旦创建下游管道，`trigger`作业就会以`success`状态完成。

`strategy: depend`将自身状态从触发的管道合并到源作业。



###### 父子管道

在同项目中创建 ci/child01.yml:

```yaml
stages:
  - build

child-a-build:
  stage: build
  script: 
    - echo "hello3a"
    - sleep 10
```

父管道：
```yaml
staging2:
  variables:
    ENVIRONMENT: staging
  stage: deploy
  trigger: 
    include: ci/child01.yml  
    strategy: depend
```

## image、services

启用image，需要安装docker runner：

```bash
gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  # runner的基础镜像
  --docker-image alpine:latest \
  --url "http://192.168.1.200:30088/" \
  --registration-token "JRzzw2j1Ji6aBjwvkxAv" \
  --description "docker-runner" \
  --tag-list "newdocker" \
  --run-untagged="true" \
  --locked="false" \
  --docker-privileged \ 
  --access-level="not_protected"
```

修改gitlab-runner配置对象，加速docker访问：

```bash
[[runners]]
  name = "docker-runner"
  url = "http://192.168.1.200:30088/"
  # 访问token
  token = "xuaLZD7xUVviTsyeJAWh"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
  [runners.docker]
    pull_policy = "if-not-present"
    tls_verify = false
    image = "alpine:latest"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
```



##### image语法

在cicd文件中，可以定义全局的image、job级别的image，一旦定义以后，pipeline将在docker镜像的容器中进行操作：

```yaml
#image: maven:3.6.3-jdk-8

before_script:
  - ls
  
  
build:
  # 基本maven镜像运行这个job
  image: maven:3.6.3-jdk-8
  stage: build
  tags:
    - newdocker
  script:
    - ls
    - sleep 2
    - echo "mvn clean "
    - sleep 10

deploy:
  # 使用runner默认的image，启动容器运行job
  stage: deploy
  tags:
    - newdocker
  script:
    - echo "deploy"
```



##### services

使用docker runner，可以起到类似于（docker-compose的方法）

```yaml

services:
  - name: mysql:latest
    alias: mysql-1

```

在上面的文件中，本服务就可以通过 alias 去访问数据库服务，而不是host+port。

## 集成SonarQube代码扫描

在runner的服务器上安装sonnar，然后定义一个模板

```bash
.codeanalysis-java:
  stage: code_analysis
  tags:
    - build
  script:
    - echo $CI_MERGE_REQUEST_IID $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME  $CI_MERGE_REQUEST_TARGET_BRANCH_NAME
    - "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=${CI_PROJECT_NAME} \
                                      -Dsonar.projectName=${CI_PROJECT_NAME} \
                                      -Dsonar.projectVersion=${CI_COMMIT_REF_NAME} \
                                      -Dsonar.ws.timeout=30 \
                                      -Dsonar.projectDescription=${CI_PROJECT_TITLE} \
                                      -Dsonar.links.homepage=${CI_PROJECT_URL} \
                                      -Dsonar.sources=${SCAN_DIR} \
                                      -Dsonar.sourceEncoding=UTF-8 \
                                      -Dsonar.java.binaries=target/classes \
                                      -Dsonar.java.test.binaries=target/test-classes \
                                      -Dsonar.java.surefire.report=target/surefire-reports \
                                      -Dsonar.branch.name=${CI_COMMIT_REF_NAME}"
  artifacts:
    paths:
      - "$ARTIFACT_PATH"
```

流水线：
```yaml
include:
  - project: 'cidevops/cidevops-gitlabci-service'
    ref: master
    file: 'jobs/build.yml'
  - project: 'cidevops/cidevops-gitlabci-service'
    ref: master
    file: 'jobs/test.yml'
  - project: 'cidevops/cidevops-gitlabci-service'
    ref: master
    file: 'jobs/codeanalysis.yml'

variables:
  BUILD_SHELL: 'mvn clean package  -DskipTests'  ##构建命令
  CACHE_DIR: 'target/'
  TEST_SHELL : 'mvn test'                                   ##测试命令
  JUNIT_REPORT_PATH: 'target/surefire-reports/TEST-*.xml'   ##单元测试报告
  # 代码扫描
  SCANNER_HOME : "/usr/local/buildtools/sonar-scanner-3.2.0.1227-linux"
  SCAN_DIR : "src"
  ARTIFACT_PATH : 'target/*.jar'                            ##制品目录

  
cache:
  paths:
    - ${CACHE_DIR}
    
stages:
  - build
  - test
  - code_analysis


build:
  stage: build
  extends: .build
  rules:
    - when: on_success


test:
  stage: test
  extends: .test
  rules:
    - when: on_success

  
code_analysis:
  stage: code_analysis
  extends: .codeanalysis-java
```

