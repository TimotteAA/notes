# Linux笔记

## ssh连接

啥是ssh：
![image-20240112095755261](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240112095755261.png)

#### ssh连接配置

```BASH
# ssh连接设定
# 服务器ip、开放的ssh端口号、密码
# 一般而言powsershell、linux自带了ssh
ssh [user]@[ip]

# 输入密码


# 退出bye bye
exit
```

在刚安装的虚拟机中，利用ifconfig查看网卡信息：


![image-20240112101141781](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240112101141781.png)

```bash
# 尝试ssh连接
ssh timotte@192.168.247.129

# 输入密码
# 连接成功
[timotte@localhost ~]$


```



### 包管理工具

```bash
## ubuntu和debian
## dpkg
dpkg -i package.deb
dpkg -r package

## apt
apt-get install package # 安装package软件包
apt-get remove package # 卸载package软件包

## centos推荐yum或者dnf
yum install package
yum remove package

## centos8
dnf install package
dnf remove package
```

本笔记使用yum进行学习，下图说明了yum的安装过程

![image-20240112102321673](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240112102321673.png)

为了提高下载速度，配置国内镜像源：

```BASH
# 切换到yum镜像配置
cd /ect/yum.repos.d/
# 下载腾讯云的centos_8base.repo，命名为CentOS-Base.repo
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.cloud.tencent.com/repo/centos8_base.repo

# 更新缓存信息
yum makecache

## 由于centos8不维护了，需要下面的命令才能访问到之前归档的镜像
dnf --disablerepo '*' --enablerepo extras swap centos-linux-repos centos-stream-repos
dnf distro-sync
```



## 基础命令

ls，查看指定目录下的内容：
```BASH
ls
ls -l  # 显示当前目录下内容的详细信息
ls -la # 比-l多了隐藏文件信息
ls -lha # 与ls -l输出内容一致，多了文件大小的转换
ls -lt # 按时间排序
ls -R #递归显示当前目录中的所有文件和子目录
ls -l [dir_name] # 显示指定文件夹下的文件
```

cd 切换目录

```BASH
cd ~ # 切换到home目录
cd .. # 上一级目录
cd - # 切换到上一次（过来的）目录
cd [dir_name] # 切换到指定目录
```

cp 复制目录

```bash
cp [file] [new_file]  # 将file拷贝到new_file
cp -r [dir] [new_dir] # 将dir拷贝生成new_dir，子文件夹也会拷贝
cp -i [file] [new_file] # 将file拷贝生成[new_file]，new_file存在则询问
cp -f [file] [new_file] # 比-i直接覆盖
cp -b [file] [new_file] # new_file存在则备份
# cp默认是cp -i
```

mv 移动

```bash
mv [src_file] [dest_file] # 把src_file移动到dest_file
mv -i # 已存在则询问
mv -f # 强制覆盖
mv [src_file] [dir_name]
mv [dir_name]/* . # 把dir_name下的所有内容移动到当前目录下

# 已有test.txt
mv test.txt test-new.txt # 看上去是移动，其实改了个名字
mv test/ test2/ # 把test/文件夹，重命名为 test2/
```

cat 显示文件内容到终端上

```BASH
cat [file]       # 输出file文件的内容到终端里
cat -n [file]    # 显示行号
cat -n [src_file] > [dest_file] # 将src_file的内容输出到dest_file
cat -n [src_file_1] [src_file_2] > [dest_file]
cat /dev/null > [dest_file] # 清空内容
```

head 查看文件开头部门的内容

```BASH
head -n [lines] file # 查看前n行
head -c [chars] file # 查看前n个字符
```

tail，查看文件尾部的内容

```bash
tail -n [lines] file # 查看前n行
tail -c [chars] file # 查看前n个字
```



