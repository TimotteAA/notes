# ubuntu学习笔记

本笔记基于vmware + ubuntu22进行学习

* ubuntu sever镜像地址：https://releases.ubuntu.com/22.04.3/ubuntu-22.04.3-live-server-amd64.iso

运行虚拟机软件的操作系统 我们称之为 `Host OS`， 虚拟机 软件里面的操作系统称之为 `Guest OS`

在安装时，需要配置下清华镜像源以加速下载：

* https://mirrors.tuna.tsinghua.edu.cn/ubuntu/



#### 登录方式

1. 直接在服务器上用命令行登录：

   ![image-20240209085714983](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/undefinedimage-20240209085714983.png)

​     在命令行中，先输入安装时配置的用户名**timotte**，然后输入密码**123456**

2. ssh远程登录（比如登录某个云上的主机）

   此处推荐使用Putty：https://the.earth.li/~sgtatham/putty/0.73/w64/putty.exe

​	由于是安装ubuntu虚拟机时，默认使用的是nat网络，需要在vm中转换成桥接网络：

​	![image-20240209090326017](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/undefinedimage-20240209090326017.png)



## shell和linux命令

在远程终端是通过Linux命令（command）来操作计算机的。

而Linux主机上谁来接受我们输入的命令，并执行命令的呢？ 那就是 **Shell** 程序。

Shell有很多种，标准shell (sh), Bourne Again SHell (bash), Korn shell (ksh), C shell (csh)，现在主流的Linux的缺省是bash，所以我们主要针对它进行说明。

一个linux命令的格式如下：
```BASH
COMMAND arg1 arg2 ....
```

Linux命令由一个命令（command）和零到多个参数构成，命令和参数之间，以及参数与参数之间用空格隔开。

Linux的命令区分大小写，且命令和参数之间必须隔开。



### 注销（退出linux系统）

这里使用putty来远程ssh连接ubuntu，在不使用时，使用**exit**命令来退出登录

### 关机与重启

```bash
# 关机
poweroff
# 重启
reboot
```

### root用户登录

在centos8中，在安装时提供了root用户的命令配置，供开发者直接用root用户来操作机器。

Ubuntu 不想让大家直接使用root账号登录， 因为权限太大，误操作可能有很大危害。

如果对自己有足够的自信，可以用下面的方式修改root账户的密码：
```BASH
# 尝试以超级管理员的身份，修改root账户的密码
# 前提是登录的账户具有超级管理员的权限
timotte@timotte:~$ sudo passwd root
#输入root账户的密码，密码为root
New password:
Retype new password:
# 修改成功
passwd: password updated successfully
timotte@timotte:~$

# 从当前账户切换成root超级管理员
timotte@timotte: su - root
# 切换成功
root@timotte:
root@timotte: poweroff
# 可以看到，此时成功关机了


```

## 文本编辑：vi

在linux中，提供了windows中记事本功能的编辑器vi来编辑文本：

```BASH
vi a.txt
```

vi分为三种模式

1. 命令模式
2. 插入模式
3. 底行模式



#### 命令模式

在用vi打开文件后，默认进入的就是命令模式：

![image-20240209133818970](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/undefinedimage-20240209133818970.png)

命令模式下的操作：

1. **光标移动**

   vi提供了hijk的键位来移动光标：

   ```bash
   键盘h  - 左移光标，无法移动会提示
   键盘l  - 右移光标，无法移动会提示
   键盘j  - 下移光标，无法移动会提示
   键盘k  - 上移光标，无法移动会提示
   ```

   当然也可以使用方向键来移动光标

   除此之外，还提供了更快捷的方式来移动光标：

   ```BASH
   按0或^ － 光标移动到所在行的行首
   按$    － 光标移动到所在行的行尾
   输入gg － 把光标移到文件开始位置
   输入G  － 把光标移到文件末尾
   输入Ctrl + f  －  往下翻一页
   输入Ctrl + b  －  往上翻一页
   ```

2. **删除操作**

   ```bash
   按x  － 删除光标所在字符
   按dd － 删除光标所在行
   按dw － 删除光标所在处到词尾的内容
   按d$ － 删除光标所在处到行尾的内容
   ```

3. **复制操作**

   ```bash
   按yy – 复制光标所在的行
   按p – 黏贴
   按v，然后移动光标，可以选择内容，再按y复制选中的内容
   按u – 撤销刚才所做的操作
   按Ctrl+r – 重做被撤销当前所做的操作
   按. – 重复刚才所做的操作
   ```

   

#### 插入模式

从命令模式进入插入模式编辑文本，一共有三张fangfa :

```BASH
# 下面的按键都是在命令模式下完成的
按键a  - 在当前光标字符后插入内容
按键i  - 在当前光标字符处插入内容
按键o  - 在当前光标所在的下一行开始插入内容
```

插入模式退出：按下esc即可

#### 底行模式

共有两种方式进入底行模式：

`:wq` 保存文件并退出

`:q` 不保存文件，并退出，如果文件做了修改，但有不想保存，需要用:q!

`:q!` 不保存文件，强制退出

`:w` 只保存文件，但是不退出vi，可以切换到输入模式下面继续编辑文件

`:set nu` 显示行号

`:set nonu` 不显示行号

`/abc` 在文件中查找abc字符。按 `n` 不停的往下查找，按 `N` 往上查找

`:1,$s/string/replace/g` 替换功能，把文件中的string,替换为replace 按Esc键，切换到命令模式

:1,$指的是从文件开始到文件结束

## 文件系统

注：在linux的终端中，蓝色的表示是个目录

#### 查看当前工作目录

在linux中，可以用**pwd**命令查看当前shell所在的工作目录，也就是我们所在的目录。

当用户刚刚登录的时候，所处的目录是用户的 **home目录**

对于**root**用户，就是`/root`

对于其他的用户，则是在`/home/用户名`

比如你是用户 byhy，home目录通常就是 `/home/byhy`

```bash
root@timotte:~# pwd
/root
root@timotte:~#
```

#### 改变工作路径

linux使用 `cd` 命令来改变当前的目录：

```BASH
# 进入根目录下的/tmp目录
cd /tmp
# 进入当前目录下的package目录
cd package
# 进入根目录
cd /
# 返回home目录
cd ~
# 返回上一次所处的目录
cd -
```

### 绝对路径和相对路径

* 绝对路径：绝对路径 **开始于根目录**，沿着目录层级，一直到达所期望的目录或文件。

​	![image-20240210085209950](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/undefinedimage-20240210085209950.png)

* 相对路径：相对的是当前目录，在使用相对路径的过程中， 经常用到一对特殊符号 `.` (点) 和 `..` (两个点)。

  符号 `.` 指的是当前目录，`..` 指的是当前目录的父目录。

  根据上面的图，假如当前目录为 jono ，如果要用相对路径切换到photos,就这样写 `cd ./photos` ，也可以直接写 `cd photos` ，



#### 查看目录的文件信息：

查看目录内容 或者某个文件的属性 使用命令 `ls`

```BASH
# 简单的查看
root@timotte:/tmp# ls
snap-private-tmp
systemd-private-f221143a0e54414497e1af523b146da5-ModemManager.service-vq7jEM
systemd-private-f221143a0e54414497e1af523b146da5-systemd-logind.service-EhrZgN
systemd-private-f221143a0e54414497e1af523b146da5-systemd-resolved.service-VS1gOW
systemd-private-f221143a0e54414497e1af523b146da5-systemd-timesyncd.service-PENZL2
vmware-root_686-2689274894

# 查看特定目录下的信息
root@timotte:/tmp# ls /home/timotte
a.txt  b  b.txt

# 查看文件的更多信息
[byhy@iztqz ~]$ ls -l
total 8
-rw-rw-r-- 1 byhy byhy   13 May 10 10:36 byhy.txt
drwxrwxr-x 2 byhy byhy 4096 Apr 17 11:55 frontend

```

各个字段的含义如下：

`-rw-r--r--` 文件的访问权限。第一个字符指明文件类型。在不同类型之间， 开头的“－”说明是一个普通文件，“d”表明是一个目录。其后三个字符是文件所有者的 访问权限，再其后的三个字符是文件所属组中成员的访问权限，最后三个字符是其他人的访问权限。这个字段的含义在后面用户权限那一章会详细讲解。

`1` 文件的硬链接数目。

`byhy` 文件所有者的用户名。

`byhy` 文件所属用户组的名字。

`13` 以字节数表示的文件大小。

`May 10 10:36` 上次修改文件的时间和日期。

`byhy.txt` 文件名。

**-a** 可以查看隐藏文件

#### 查看文件内容cat、less

对于较小的文件，可以直接用 **cat** 命令把文件内容输出到终端上：
```BASH
root@timotte:~/test# cat index2.js
console.log("hello")
root@timotte:~/test#

```

而对于较长的文件，推荐使用**less**命令来检索文件内容：

```BASH
less xx.log 
```

支持上下键的文件翻页，``/keyword``的关键词搜索

`-N` 显示行号

 注意，不太推荐用 vi 来查看文件内容。



#### 创建目录

mkdir 命令是用来创建目录的。

例如

```
mkdir byhy1
```

会在当前目录下，创建一个名为"byhy1"的目录，而

```
mkdir byhy1 byhy2 byhy3
```

会创建三个目录，名为 byhy1, byhy2, byhy3。

如果我们要创建好几层的目录，比如 /root/byhy/python/lesson1,

直接这样写命令 `mkdir /root/byhy/python/lesson1`

shell 会报错，因为系统中可能还 没有 /root/byhy/python 这个目录。

一种方法是：我们 依次 创建 每一级目录， 像这样

```py
mkdir /root/byhy
mkdir /root/byhy/python
mkdir /root/byhy/python/lesson1
```

更简单的方法是，使用参数 -p

```py
mkdir -p /root/byhy/python/lesson1
```

#### 删除文件和目录

rm 命令用来 删除 文件和目录。

rm 命令后面 直接加上要删除的文件，比如

```py
rm file1 file2
```

当我们要删除目录的时候 ，需要加上 -r 选项，否则会报错，如下所示

```py
$ rm dir1
rm: cannot remove 'dir1': Is a directory
```

要这样写

```py
$ rm -r dir1
```

-r 参数 也可以详细的写成 –recursive 。 它表示要 递归地删除文件。 这意味着，如果要删除一个目录，而此目录 又包含子目录，那么子目录也会被删除。

通常 -r 和 -f 参数会一起使用。

-f 参数 也可以详细的写成–force 。 它表示忽视不存在的文件，不显示提示信息。

比如：

```py
rm -rf file1 dir1 file2 dir2
```

如果文件 file1，或目录 dir1 不存在的话，rm 仍会继续执行，不会报错

执行 rm 操作 要非常小心， rm 不像Windows里面有回收站，一旦你用 rm 删除了一些东西，想再恢复是相当的麻烦的。

### 通配符

在linux中，支持通配符来批量操作文件，比如当前目录有下面的这些文件：

```BASH
byhy1.txt  file1.jpg  file2.jpg  file3.jpg  
file5.jpg  hy2.jpg   hyby.jpg  

```

我们要删除 其中 所有 以file开头， 扩展名为 .jpg 的文件，怎么办？

一种方法是一个个的删除：

```BASH
rm -f file1.jpg file2.jpg file3.jpg file5.jpg
```

更快捷的方法是利用linux提供的通配符：

```BASH
rm file*.jpg
```

常用的通配符有下面：

`*` 匹配任意 `多个` 字符（包括零个或一个）

`?` 匹配任意 `一个` 字符（不包括零个）

`[abcd]` 匹配abcd中任意一个字符

下面是一些示例的用法

`*` 表示 所有文件（或目录）

`by*` 表示 文件名以“by”开头的文件（或目录）

`by*.py` 表示 以"by"开头，中间有任意多个字符，并以".py"结尾的文件（或目录）

`byhy????` 表示 以“byhy”开头，其后紧接着 `4个字符` 的文件（或目录）

`[byh]*` 表示 文件名以"b",“y”,或"h"开头的文件（或目录）

`byhy[0-9][0-9]` 表示 以"byhy"开头，并紧接着2个数字的文件（或目录）



**对于linux命令，只要参数是文件，都可以使用通配符：ls、cp、mv**

#### 复制文件和目录

cp 命令， 用来 复制文件或者目录。

假设 byhy1 是一个文件，我们可以 这样

```py
cp byhy1 byhy2
```

如果 byhy2 这个文件 不存在， 上面的命令会创建一个新文件 byhy2 ，并且把 byhy1内容 拷贝到byhy2中。

如果 byhy2 已经存在了， 上面的命令会直接把 byhy1内容 拷贝到byhy2中， 就是说会覆盖byhy2 原来的内容。

如果我们要拷贝的是一个目录， 则 需要加上 -r 选项

比如

```py
cp -r  frontend frontend2
```

指定 -r 或者 –recursive 会递归地复制目录及目录中的内容。就是说，如果 frontend 里面有好多级子目录和文件，全部都会被拷贝过去。

#### 移动文件、重命名文件

**mv** 命令用于文件的移动、重命名

使用方法和**cp**命令很像：

```BASH
# 如果t1存在，则重命名为t2
# 否则拷贝t1的内容，到新的t2文件中
mv t1 t2
```

假设 dir1 是一个目录，我们可以执行

```py
mv  dir1 dir2
```

如果目录 dir2 不存在，就会把 dir1目录 改名为 dir2。

如果目录 dir2 存在，就会把 dir1（及它的内容）到目录 dir2。

假设 byhy1 是一个文件，dir1 是一个目录，我们可以执行

```py
mv  byhy1 dir1 dir2
```

这样写的话， 如果目录 dir2 存在，就会 移动文件 byhy1 和 目录 dir1（及它的内容）到目录 dir2。

#### 在文本中查找字符串 grep

除了用cat、less等命令直接查看文件内容，也可以用grep来直接在文件中搜索内容：

```BASH

root@timotte:~/test# grep 105 my_log_file.log
Log 105 - 2024-02-10 01:05:33
# 在文件中搜索105的关键词

# 如果希望现实行号，则可以跟上-n

root@timotte:~/test# grep 105 my_log_file.log -n
105:Log 105 - 2024-02-10 01:05:33

# 如果希望查询一个句子，则需要使用双引号：

root@timotte:~/test# grep "Log " my_log_file.log -n

```



## 用户系统

Linux 系统有个特殊的账号 `root` 。 通常是 系统管理员使用该账号，就像Windows里面的Administrator账号一样。

root账号 是系统安装好就有的，具有系统中最高的权限，比如：可以创建其它账号、停止其它用户进程、修改一些Linux系统配置文件、访问其它用户文件。

而其它账号则是有权限限制的，上面说的root用户的特权，其它账号通常是没有的。

之所以要有这样的权限区别，就是防止一些用户不小心（或者故意）做出的破坏。

Linux系统中，每个用户账号 都对应一个 用户 ID。

用户ID 就是一个数字，因为计算机处理数字更加方便。

用户的 ID 与账号的信息就存储在文件 /etc/passwd 当中。

另外 每个用户可以属于一个或者多个 `用户组` ，每个用户组对应一个 `GroupID` 。

GroupID 的信息存储在文件 /etc/group 当中。

系统管理员可以对某个用户组中的所有用户进行统一管理，比如分配权限等。

#### 添加用户

**adduser**命令可以用于添加一个用户：

```BASH
root@timotte:/# adduser t1
Adding user `t1' ...
Adding new group `t1' (1003) ...
Adding new user `t1' (1002) with group `t1' ...
Creating home directory `/home/t1' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for t1
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y

```

这样就成功创建一个新的用户**t1**

```bash
cat /etc/passwd # 查看用户配置
t1:x:1002:1003:,,,:/home/t1:/bin/bash

```

每一个 : 代表一字段：

```bash
用户名
登录密码 
用户ID 
用户Group ID
描述信息
用户的home目录
该用户缺省shell
```

#### 查看用户组

在上面创建好 t1 用户后，默认的也创建了一个用户组 t1

```bash
root@timotte:/# groups t1
t1 : t1
root@timotte:/#

```

**groups** 命令可以查看用户的用户组

当然也可以在用户创建时指定用户组：

```BASH
adduser --ingroup root t2  # 在创建t2用户的同时，把它加入到 root 这个用户组里面了
root@timotte:/# groups t2
t2 : root

```



#### 设置密码

当需要修改密码时，可以使用 **passwd** 命令：

```bash
root@timotte:/# passwd t2
New password:
Retype new password:
passwd: password updated successfully

```

#### 删除用户 deluser

deluser 命令可以用于删除用户：
```BASH
root@timotte:/# deluser t1
Removing user `t1' ...
Warning: group `t1' has no more members.
Done.
```

#### 切换用户 su - 

当需要切换到别的用户时，可以使用 su 命令：

```BASH
# 切换
root@timotte:/ su - timotte
timotte@timotte:~$ echo 1
1
timotte@timotte:~$ sleep 2
# 切换
timotte@timotte:~$ su - timotte2
Password:
# 退出timotte2
timotte2@timotte:~$ exit
logout
# 退出后，按下方向上，可以看到登录timotte时的shell记录
timotte@timotte:~$ su - timotte2
```

su切换用户相当于一个栈（保留当前用户的shell栈），exit相当于移除当前的用户

#### 查看用户组

```bash
root@timotte:/# groups
root
root@timotte:/# groups t2
t2 : root
root@timotte:/# groups timotte
timotte : timotte adm cdrom sudo dip plugdev lxd

```

其中第一个 timotte是timotte用户的 `主用户组（primary group）` ，其它的是 `从用户组（secondary group）`

`/etc/passwd` 文件里面记录的是用户的主用户组：

```bash
timotte2:x:1001:1002:,,,:/home/timotte2:/bin/bash
t2:x:1003:0:,,,:/home/t2:/bin/bash

```



要查看某个组里面有哪些用户

可以直接看/etc/group 文件，

更快速的，可以这样

```py
grep '^sudo' /etc/group
```

但是这样只能查看到从用户组为该组的用户。

据[这个帖子](https://serverfault.com/questions/605812/etc-passwd-shows-user-in-a-group-but-etc-group-does-not)的说明，/etc/group里面记录的从用户组用户。

#### 添加用户组

增加一个新的用户组使用 `addgroup` 命令。

比如：

```
addgroup byhyusers
```

此命令向系统中增加了一个新组byhyusers。

#### 删除用户组

如果要删除一个已有的用户组，使用 `delgroup` 命令

比如

```
delgroup byhyusers
```

此命令从系统中删除组byhyusers。

#### 改变用户所属组 usermod

如果要将一个用户从组1改到组2，需要root用户使用 `usermod` 命令，其格式如下：

例如：下面的 命令将用户byhy的 主用户组（primary group）设置为 g1 。

```
usermod –g g1 byhy
```

注意：主用户组只能有一个





下面的命令将用户byhy用户的 从用户组（secondary group）设置为：g2，g3

```
usermod –G g2,g3 byhy
```

从用户组 可以有多个。





下面的命令给用户byhy `再添加` 两个从用户组（secondary group） g2，g3 ，原来所属的从用户组仍然也在

```
usermod -a -G g1,g2 byhy
```

注意： 有 `-a` 参数是**添加**从用户组， 没有 `-a` 参数是**设置**从用户组

### linux文件权限

对于linux`文件`，读写、运行权限主要指的是：

1. 有读权限，表示该用户可以读取文件的内容，
2. 有写权限，表示该用户可以修改文件内容，
3. 有执行权限, 表示该用户可以运行该文件（当然该文件应该是可执行文件）



对于linux `目录`，读写、运行权限主要是：

1. 有读权限，表示该用户可以查看该目录里面的内容，

2. 有写权限，表示该用户可以在该目录里面 创建 和删除 文件，

3. 有执行权限, 表示该用户可以使用 cd命令，进入该目录



可以用ls -l 来查看某个文件或目录的权限：

```BASH
root@timotte:~# ls -l
total 12
-rw-r--r-- 1 root root    7 Feb 10 06:25 2.go
drwx------ 3 root root 4096 Feb  9 00:54 snap
drwxr-xr-x 4 root root 4096 Feb 10 01:26 test

```

* 第一位值得是是否是一个文件：

- 文件的所有者，英文叫 owner ， 也就是 该文件的 创建者
- 文件归属的用户组里面的用户 ，英文叫 grouper，
- 其他用户 (非owner和非grouper)

对于上面的 **2.go** 文件，可以看到：

1. 2-4字符的 rw- ，表示文件拥有者root权限是可读、可写、不可运行（非可执行文件）
2. 5-6字符的r--，对于同组root的其他用户：可读、不可写、不可执行
3. 7-8字符的r--，对于其他组的用户：可读、不可写、不可执行



```bash
timotte@timotte:~$ cd /root
-bash: cd: /root: Permission denied

```

默认下，可以发现 /root 目录是无法cd进入的：

```BASH
drwx------   5 root root  4096 Feb 10 06:25 root

```

#### 修改文件权限

文件的所有者或者root用户可以修改文件的访问权限

用chmod命令修改文件的存取权限，chmod命令的格式如下：

```
chmod  [who][op][permission]  file...
```

具体的规则：

who项表示用户类型，它的内容为以下一项或多项:

```py
u	拥有者(user --- owner)
g	与拥有者同一组的用户(group)
o	其他人(other)
a	所有人(all)
```

op项表示动作:

```py
+	表示要加上permission指定的权利
-	表示要取消permission指定的权利
```

permission项为存取权限，它的内容为以下一项或多项：

```py
r	表示可读
w	表示可写
x	表示可执行
```

比如：

`chmod u+w file1` ，该命令添加了 拥有者对file1文件的写权限

`chmod u-x file1 `，该命令去掉了 拥有者对file1文件的执行权限

`chmod ug+rwx file1` ，该命令添加了 拥有者和同组用户 对file1文件的 读、写、执行权限

`chmod a+rwx file1`，该命令添加了 所有人 对file1文件的 读、写、执行权限

`chmod o+rx /root` ， 该命令添加了 其它组 对 目录 `/root` 的可读可执行权限。 对于目录来说 ，可执行权限意味着，用户可以 `cd 进入` 到这个目录。

`chmod -R o+rx /root` ， 参数 `-R` 表示递归的意思，该命令执行结果是：其它用户可以对 `/root 目录以及所有它的子目录、子文件` 都有 可读可执行权限 。



参考：

```BASH
# 切换到root
timotte@timotte:~$ su - root
Password:
root@timotte:~# cd /home/timotte
root@timotte:/home/timotte# ls -l
total 12
-r--rw-r-- 1 timotte t2   52 Feb 10 07:29 a.txt
drwxrwxr-x 2 timotte t2 4096 Feb  9 06:20 b
# 原来创建者是可读的，现在不可读了
-rw-rw-r-- 1 timotte t2    5 Feb  9 06:20 b.txt
# 修改timotte，不可读b.txt
root@timotte:/home/timotte# chmod u-r b.txt
root@timotte:/home/timotte# su - timotte
timotte@timotte:~$ cat b.txt
cat: b.txt: Permission denied

```

#### 改变文件的所有权 chown

chown将指定文件的拥有者改为指定的用户或组。

该命令的参数中，用户可以是用户名或者用户ID；组可以是组名或者组ID。文件是以空格分开的要改变权限的文件列表，支持通配符。

系统管理员经常使用chown命令，在将文件拷贝到另一个用户的名录下之后，让用户拥有使用该文件的权限.

注意：必须是有root权限的用户才能改变文件所有者。

改变文件所有者的命令格式如下:

```
chown [选项]... [所有者][:[组]] 文件...
```

例如：

`chown byhy test1` ，就把文件test1的所有者变更为用户byhy

`chown byhy:byhy test1` ，就把文件test1的所有者变更为用户byhy，所属组变为byhy

`chown -R byhy dir1` ，就把目录dir1以及下面所有的子目录和文件的所有者变更为用户byhy

也可以用命令chgrp改变文件所有者组，格式如下:

```
chgrp [选项] [组] [文件]
```

注意：必须是有root权限的用户才能改变文件组别的归属

比如:

`chgrp byhy test1` ， 就把文件test1的用户组变更为组byhy



#### sudo，以root的权限运行

有的程序需要做一些特权操作，比如前面讲的用户账号管理：adduser、deluser。

通常我们要执行这样的程序必须以root用户登录去执行。

但是，我们有时却希望给某几个信任的用户授予这样的权限，允许他们某次可以申请以以root权限执行该程序。

Windows里面的 `以管理员权限` 运行某个程序，就是这样。

Linux上也有这种方法，就是使用命令 `sudo`

比如

```py
sudo adduser byhy3
```

就是申请以root权限运行 `adduser byhy3` 这个命令。

当然，很明白，不是你说申请运行就一定可以的，前提是你当前登录的账号 要在系统设置里面 允许这样做。

这个设置是 在 `/etc/sudoers` 里面配置的。 如下

```py
root    ALL=(ALL:ALL) ALL

%admin         ALL=(ALL) ALL
%sudo          ALL=(ALL:ALL) ALL

byhy2, byhy3   ALL = /sbin/shutdown, /sbin/reboot
```

通过配置这个文件，可以设置

哪些用户（或者哪些组里面的用户） ，在什么地方， 以 哪些账号的权限 ， 运行哪些程序。

一般不建议直接修改这个文件， 防止语法写错，导入整个规则被破坏。

而是使用命令 `visudo` 命令修改， 这个命令可以对错误的修改做检查，防止意外。

通常，我们会给某个组设置规则， 然后只需要把某些用户加入组中即可。

比如，Ubuntu上可以把某个用户加入 sudo 这个组， 既可以拥有 任何地方、以任何账号的权限、运行任何程序 的能力。

把用户加入组的命令，就是使用 usermod， 上面已经讲过。

注意：通常修改组后，该账号需要重新登录一下，才有组的sudo 权限。



## 进程管理

在Linux中，你正在运行的交互式命令行程序 Shell， 它就是一个进程。

我们可以用命令 ps 查看进程信息的命令。

```bash

timotte@timotte:~$ ps
    PID TTY          TIME CMD
   6219 pts/0    00:00:00 bash
   7228 pts/0    00:00:00 ps
```

#### 进程的创建与查看

Linux中，一个进程A里面可以创建出一个新的进程B，进程A就叫做进程B的 `父进程` (parent process)。

进程B叫做进程A的子进程（child process）。

最典型的例子，我们在shell中运行的程序（命令），都是shell进程创建的，所以shell进程就是他们的父进程。

Linux中，主要是通过ps命令来查看进程信息的，我们运行命令**ps -f** ，结果如下所示：

```BASH
timotte@timotte:~$ ps -f
UID          PID    PPID  C STIME TTY          TIME CMD
timotte     6219    6218  0 Feb11 pts/0    00:00:00 -bash
timotte     7230    6219  0 00:18 pts/0    00:00:00 ps -f

```

可以看到，ps 的父进程是bash

下面列举了常用的 ps命令 的例子：

`ps` 显示和当前终端有关的进程信息

`ps -u byhy` 显示byhy用户所创建的进程信息

`ps -f` 详细显示每个进程信息

`ps -e` 显示所有正在运行的进程信息

`ps -ef` 显示当前系统所有的进程

`ps –ef | grep python`  利用管道符，把ps -ef的结果作为grep的输入， 查找python进程

#### 进程的前后台转换

Linux终端通过Shell程序来接收用户输入的命令，并且执行命令。

我们在Shell里正在执行的，和用户进行人机交互的进程叫 `前台进程` (foreground process)

前台进程可以接收键盘输入并将结果显示在显示器上。

用户敲入什么命令，shell就会启动对应的程序，运行在 `前台` 。

比如下面的python代码：

```PYTHON
while True:    
    info = input("please input something:")
    print("you input:%s\n\n" % info)
```



然后使用命令 `python3 t1.py` 运行。

可以发现及时这个 python 程序变成了前台进程，接收用户的输入。

有些程序运行时，并不需要和用户进行交互，也就是说，不需要用户输入什么内容。 比如一个日志分析程序，一个定时清理磁盘文件的程序。

比如，下面这样的一个Python程序 t2.py：

```python
import time

while True:    
    print("execute a task ...")
    time.sleep(2)
    print("done, wait for an hour to proceed...")
    time.sleep(3600)
```

我们可以执行命令 `python3 t2.py` 运行它

这样的程序，运行期间，如果在前台执行，我们只能等待它结束，不然我们没法执行下个程序。

但是既然不需要用户输入信息，在前台执行，没有太大意义，我们应该要让它在 `后台` 执行。

要让它在后台运行，启动时只需在命令行的最后加上“&”符号。

比如 `python3 t2.py &`



后台运行的进程我们叫后台进程(background process)，或者后台任务 ，它不直接和用户进行交互的进程。用户一般是感觉不到后台进程程序的运行。



#### nohup，后台不退出

我们可以执行命令的时候，使用 & 结尾使进程在后台运行。

但是如果终端关闭，那么程序也会被关闭，因为shell会发送SIGHUP信号给这些进程。进程接收的该信号，如果没有特别的处理，缺省就会**结束运行**。

为了避免这种情况，那么我们就可以使用 `nohup` 这个命令。

比如我们有个test.sh 需要在后台运行，并且希望在后台能够一直运行，即使关闭了终端，也不退出。那么就使用nohup：

```bah
nohup python3 t2.py &
```



#### 进程的终止：kill -9

进程的终止一般有两种方式

1. 主动退出：

   有的进程执行完一段任务后，就自行退出了。

   比如上面的ps命令，它执行完查看进程信息的任务后，就会结束。

   也有的不是自动退出，而是用户操作它，让它退出。 比如 我们在Shell进程中运行exit命令后，该Shell进程就会退出。

   也有的是异常退出，比如程序有个bug（比如代码里面有除以0的指令），该程序无法执行下去，也会终止。

2. 被强行杀死：

   有的进程一直不结束，如果用户觉得该进程应该被强行结束了，该怎么办呢？

   对于一个前台进程，要结束它，我们只需要按组合键： `Ctrl + C` 。

   对于一个后台运行的进程 ，如果用户觉得该进程应该被强行结束，可以使用 `kill -9` 命令强行杀死该进程。

   比如上面的 `python3 t2.py` 命令运行的进程。 我们可以先用ps命令查出它的进程PID：

   ```BASH
   # 后台运行着python t2.py
   timotte@timotte:~$ ps -f
   UID          PID    PPID  C STIME TTY          TIME CMD
   timotte     7447    7446  0 00:32 pts/2    00:00:00 -bash
   timotte     7465    7447  0 00:35 pts/2    00:00:00 python3 t2.py
   timotte     7466    7447  0 00:35 pts/2    00:00:00 ps -f
   # 主动杀死
   timotte@timotte:~$ kill -9 7465
   timotte@timotte:~$ ps -f
   UID          PID    PPID  C STIME TTY          TIME CMD
   timotte     7447    7446  0 00:32 pts/2    00:00:00 -bash
   timotte     7467    7447  0 00:35 pts/2    00:00:00 ps -f
   [1]+  Killed                  python3 t2.py
   
   
   ```

   要注意的是， 上面所示的进程启动它的用户为byhy，那么只能是用户byhy或者root用户才能杀死该进程



#### 环境变量 $PATH、echo

Shell是个特殊的进程，因为我们通过它来执行命令，启动其他的进程的。所以它是很多进程的父进程。

Shell 这个父进程有很多特性会影响到我们执行命令，其中非常重要的一个就是 `环境变量` 。

环境变量 设置了 进程运行的环境信息。

Linux 的环境变量具有继承性，即:子进程 会继承父进程 的环境变量。

我们可以用命令printenv来查看当前shell的环境变量。

- 环境变量PATH

我们可以看到环境变量有很多，通常我们最关注的一个就是环境变量是其中的 PATH

因为PATH 决定了当我们敲入命令的时候，到哪里去找这个命令对应的可执行程序。

用命令 `echo $PATH` 来查看环境变量PATH的值，比如

```
[byhy@localhost ~]$ echo $PATH
/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/byhy/bin
```

当我敲入命令ps后，shell就会 `依次` 到下面的路径去寻找ps命令对应的可执行文件 /usr/local/bin -> /bin -> /usr/bin -> /usr/local/sbin -> /usr/sbin -> /sbin -> /home/byhy/bin

- 在环境变量PATH里面添加一个新的路径

方法一：执行命令 `export PATH`

比如：

```
export PATH=/test:$PATH
```

再次查看环境变量PATH的值，结果如下

```
[byhy@localhost ~]$ echo $PATH
/test:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/byhy/bin
```

说明添加PATH成功。

但是这种方式生效时临时的。再次登录的时候，PATH里面就又没有这个环境变量了。

需要一直生效，可以使用方法二。

方式二：写入到shell启动文件中

所谓shell启动文件（Startup Files）是指Shell启动时 会 `自动加载执行` 的文件。

既然这些文件会自动被shell加载执行，通常我们可以在里面放入一些 `例行` 执行的命令，比如设置一些环境变量。

bash Shell启动文件有好几个，比如 `/etc/profile` （所有用户共享）, `~/.bash_profile` , `~/.bashrc`。

[具体细节参考这个文档](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)

通常建议把某个用户 独有的 设置环境变量的 命令，放到用户家目录下面的 `~/.bashrc` 文件中。

可以在文件的结尾加入一行

```
export PATH=/test:$PATH
```

好了，下次登录的时候，PATH里面就会多出/test；

如果要对当前的shell就立即生效，可以执行命令 `source ~/.bashrc`



## 重定向（>）和管道（|）

**每个Linux进程**在启动后，通常就会打开3个文件句柄，标准输入文件（stdin），标准输出文件（stdout）和 标准错误文件（stderr）。

Linux进程，要从用户那里读入输入的信息，就是从stdin文件里面读取信息，要 输出 信息 给用户看 都是 输出到 stdout， 要 输出 错误提示 给用户看 都是 输出到 stderr。

而缺省情况下这三个文件stdin、stdout、stderr 都指向 —— 终端设备。

也就是说：

Linux进程从stdin里面读取信息其实就是从终端设备（比如终端模拟程序Putty）读取信息；

Linux进程写入信息到stdout或者stderr，其实就是打印到终端设备上。

如下图所示

![image-20240212094347015](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240212094347015.png)

比如，运行下面的Python程序，要读入信息，就是从终端读入，因为stdin指向终端设备

```py
input("please input info:")
```

再比如：下面的命令echo输出给用户看的，就是输入到终端上，因为stdout指向终端设备

```
[root@bogon ~]# echo "hello byhy"
hello byhy
```



#### stdout和stderr重定向

如果我们在Shell中输入命令的时候，使用 `>` 符号， 就可以将输入信息输出到其他文件（包括设备文件）中去。比如

```
ps > out
```

运行后，我们会发现out文件里面出现了ps的输入信息，而Putty终端窗口里面则没有任何内容打印出来了。

这个 `>` 就是 stdout 重定向符号， 它表示 stdout 不是指向 终端设备了，而是 重定向到 out 文件。 所以stdout 指向了 out 文件， 输入的信息就到 out 文件了。 终端屏幕上就没有信息了。

这时对应的示意图如下:
![image-20240212094558812](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240212094558812.png)

而Stderr的重定向符号 是 `2>` 。 注意 2 和 > 之间不能有空格。

比如我们执行下面的命令，其中hhhh是个不存在的文件

```
ps hhhh 2> err 
```

我们就会发现putty屏幕上没有任何信息，而文件 err里面则有。

如果我们要，**同时重定向**stdout和stderr到同一个文件both中，命令写法如下：

```
command &> both
```

如果我们要，重定向stdout到out文件，并重定向stderr到err文件，命令写法如下：

```
command > out 2>err
```



#### stdin重定向

对于stdin，使用`<` 重定向

用vi创建如下的python代码：
```BASH
for i in xrange(3):
    data=input()
    print ('%s+1=%s' % (data,int(data)+1))
```

该程序从stdin 读取一个数字后，显示其加1后的结果：

```bash
timotte@timotte:~$ python3 add.py
1
1+1=2
2
2+1=3
3
3+1=4

```



然后创建一个 add.txt文件：

```BASH
1
2
3
# 空行结束
```

把add.py的输入重定向为add.txt：

```BASH
timotte@timotte:~$ python3 add.py < add.txt
1+1=2
2+1=3
3+1=4

```

看到了吗？不需要我们从终端输入数字，该程序直接从文件 add.dat中读取数据并执行操作了。

这个 `<` 就是 stdin 重定向符号， 它表示 stdin 不是指向 终端设备了，而是 重定向到 add.dat 文件。 所以 stdin 指向了 add.dat 文件， 程序就从add.txt 文件读入信息 了。

这时该进程对应示意图如下：

![image-20240212095451124](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240212095451124.png)



#### 管道

上面我们曾经学习过grep命令，这个命令可以从文件中过滤出 `包含指定字符串模式` 的行。

比如我们有个文件file1，里面有的行包含了mike 这个词。如果我们想把所有包含mike的行都找出来。 可以执行命令 `grep mike file1` 。

当grep命令中没有文件参数的时候比如 grep mike, 它就会等待我们在标准输入（一般是putty终端设备）中输入一行行的内容，进行实时的过滤

在Linux操作过程中，我们经常需要 **将一个命令的输出的内容，给另一个命令作为输入的内容** 进行处理。

比如，我们想查出进程号是 6536 的进程的信息。

我们用ps -ef 可以显示出所有的进程信息，但是这里面的内容太多了，我想过滤出其中包含 6536 字符串的行。

当然可以 用重定向符号 `ps –ef > info.txt` , 然后再使用grep从 info.txt中过滤 `grep 6536 info.txt`

但是这样比较麻烦，我们可以使用 `管道操作符` 。

我们看 这个命令 `ps –ef | grep 6536`

注意其中的 竖线 | ， 这个就是管道操作符，它起的作用就是

● 将 前面的 ps –ef 命令的stdout（本来是输出到终端设备的） 重定向到一个 临时管道设备里面，

● 同时 将后一个命令 grep 6536 的stdin重定向到这个临时的管道设备。

那么这时会发生什么事情呢？ps –ef 命令的结果直接被 命令 `grep 6536` 过滤出来了。

这个过程可以用如下示意图表示：

![image-20240212100100615](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240212100100615.png)

## 常用命令

#### 安装软件包apt

在 Ubuntu上，安装软件通常使用 `apt` （全称 Advanced Packaging Tool） 软件包管理工具安装。

apt 能够从指定的 apt 源服务器自动下载安装包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

缺省的 apt 源服务器 (国内的是cn.archive.ubuntu.com)访问往往比较慢。

如果在安装Ubuntu时 ， 就更换了源，会快很多。

如果安装Ubuntu时，没有更换源，现在想换，可以修改配置文件 `/etc/apt/source.list`， 把里面源服务器域名从 `cn.archive.ubuntu.com` 改为为国内的

比如网易的 `mirrors.163.com` ， 或者阿里云的 `mirrors.aliyun.com`

步骤如下

1. 以root账号登录，或者后续命令前面加 `sudo` 以root执行
2. 执行命令 `cd /etc/apt` 进入到目录 `/etc/apt` 下
3. 执行命令 `cp sources.list sources.list.bak` 先创建备份文件，这样万一改错，可以有备份文件恢复
4. 执行 `vi sources.list` 打开文件， 准备把域名从从 `cn.archive.ubuntu.com` 替换为 `mirrors.163.com`
5. 按 冒号，进入底行模式，输入命令 `1,$s/cn.archive.ubuntu.com/mirrors.163.com/g` 进行替换
6. 确认一下域名修改正确后，输入 `:wq` 保存退出。
7. 执行命令 `apt update` ， 让修改生效

apt 命令用法

- 安装软件

```py
apt install package1 
```

安装指定的安装包package1, 比如 `apt install net-tools`

- 列出所有安装信息

```py
apt list --installed
```

显示所有已经安装的程序包

- 列出指定软件信息

```py
apt list package1
```

显示指定程序包package1的安装情况

- 删除软件

```py
apt remove package1 
```

删除程序包package1

#### 服务启动、删除 systemctl

Linux上有些软件程序是以服务的形式安装的，比如 SSH 服务、 MySQL服务、 nginx服务等。

这些 软件 的启动、重启、关闭 要使用特殊的命令

在当前的 Ubuntu 系统上，使用命令 systemctl 来 启动、重启、关闭 服务。

比如，

要查看 服务 ssh 状态， 执行命令 `systemctl status ssh`

```bash
root@timotte:/etc/apt# systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: e>
     Active: active (running) since Fri 2024-02-16 05:46:04 UTC; 17min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 870 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 901 (sshd)
      Tasks: 1 (limit: 4516)
     Memory: 6.1M
        CPU: 42ms
     CGroup: /system.slice/ssh.service
             └─901 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

Feb 16 05:46:04 timotte systemd[1]: Starting OpenBSD Secure Shell server...
Feb 16 05:46:04 timotte sshd[901]: Server listening on 0.0.0.0 port 22.
Feb 16 05:46:04 timotte sshd[901]: Server listening on :: port 22.
Feb 16 05:46:04 timotte systemd[1]: Started OpenBSD Secure Shell server.
Feb 16 05:57:13 timotte sshd[1126]: Accepted password for timotte from 192.168.>
Feb 16 05:57:13 timotte sshd[1126]: pam_unix(sshd:session): session opened for 
```



要启动 服务 ssh， 执行命令 `systemctl start ssh`

要重启 服务 ssh， 执行命令 `systemctl restart ssh`

要关闭 服务 ssh， 执行命令 `systemctl stop ssh`



##### 练习

1. apt安装nginx
2. 把nginx设置为服务
3. 在宿主机上访问nginx



```bash
# 安装nginx
root@timotte:/etc/apt# apt install nginx
# 启动nginx
root@timotte:/etc/apt# systemctl start nginx
root@timotte:/etc/apt# systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset:>
     Active: active (running) since Fri 2024-02-16 06:05:11 UTC; 21s ago
       Docs: man:nginx(8)
    Process: 2402 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_proce>
    Process: 2403 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (c>
   Main PID: 2498 (nginx)
      Tasks: 3 (limit: 4516)
     Memory: 4.6M
        CPU: 17ms
     CGroup: /system.slice/nginx.service
             ├─2498 "nginx: master process /usr/sbin/nginx -g daemon on; master>
             ├─2500 "nginx: worker process" "" "" "" "" "" "" "" "" "" "" "" "">
             └─2501 "nginx: worker process" "" "" "" "" "" "" "" "" "" "" "" "">

Feb 16 06:05:11 timotte systemd[1]: Starting A high performance web server and >
Feb 16 06:05:11 timotte systemd[1]: Started A high performance web server and a>
lines 1-17/17 (END)
^C
root@timotte:/etc/apt# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:29:0e:c2 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.1.20/24 metric 100 brd 192.168.1.255 scope global dynamic ens33
       valid_lft 258024sec preferred_lft 258024sec
    inet6 fe80::20c:29ff:fe29:ec2/64 scope link
       valid_lft forever preferred_lft forever


```

然后在本机上访问

![image-20240216140713434](C:\Users\timotte\AppData\Roaming\Typora\typora-user-images\image-20240216140713434.png)

**补充**：关于linux上的“服务”

Linux中的"服务"（Service），也常被称为"守护进程"（Daemon），是在后台运行的程序，它们通常不与用户直接交互，而是在系统启动时自动启动，或在需要时由系统管理员手动启动。服务可以执行各种任务，如文件共享、网络连接管理、日志记录等。

### Linux服务的特点

1. **自动启动：** 大多数服务被配置为随系统启动而自动启动，确保无需人工干预即可提供必要的功能。
2. **持续运行：** 服务通常在后台持续运行，直到系统关闭或服务被明确停止。
3. **不依赖终端：** 服务不依赖于用户终端或会话，即使没有用户登录系统，服务也可以运行。
4. **响应请求：** 很多服务的作用是响应网络请求、系统事件或其他服务的请求。



#### 打包和压缩

##### 打包

Linux下打包的最常用命令是tar 命令，可将多个文件、目录打包到一个文件中。

- tar 命令打包

下面是使用tar命令打包的操作演示：

在当前工作目录下面创建3个文件，使用下列命令：

```
touch  123.txt  456.txt  789.txt
```

将这3个文件放到一个文件包files.tar,使用下列命令：

```
tar  cvf  files.tar  123.txt   456.txt  789.txt
```

也可以使用通配符，如 *.txt，这样的格式代表以txt结尾的文件

```
tar  cvf  files1.tar   *.txt  
```

tar命令同样可以打包目录，假设 当前目录下 byhy是一个子目录，byhy.txt是一个文件

```py
tar cvf byhy.tar  ./byhy  byhy.txt
```

这个命令就把目录 byhy 和 文件 byhy.txt 都 打包到 文件 byhy.tar 中了。

- tar 命令解包

要 将 上面创建的 files1.tar 解压到当前目录，使用下列命令：

```
tar  xvf   files.tar 
```

- 查看tar 包内容

如果只是想查看 上面创建的 files1.tar 内容，使用下列命令：

```
tar  tvf   files.tar 
```

- 往tar 包中添加文件

如果想 在 files1.tar中 添加 新文件 newfile，使用下列命令：

```
tar  rvf  files.tar  newfile
```

注意：tar命令只是把文件、目录打包到一个文件中。 并不会压缩文件



##### 压缩

**gzip**命令用于文件的压缩与解压缩，压缩后的文件名后缀为**“.gz”**

比如

要 压缩文件abc.txt ，执行命令

```
gzip abc.txt
```

这样就产生了一个名为 abc.txt.gz 的压缩后的文件

要 解压文件abc.txt.gz，执行命令

```
gzip -d abc.txt.gz
```

- gzip 和 tar 的联合使用

tar工具与gzip工具联合使用，实现打包并压缩、解压缩并解包功能

假设 在当前目录有如下3个文件 touch 111.txt 222.txt 333.txt

我们要，打包并压缩这3个文件，放到压缩包文件 byhy.tar.gz里面，使用下面命令：

```
tar zcvf   byhy.tar.gz   *.txt 
```

解压缩并解包，使用下面命令：

```
tar zxvf  byhy.tar.gz 
```

● bzip2、zip 压缩、解压

bzip2 和 zip 也是常见的压缩解压工具， 使用方法和 gzip 类似

如下

```
---------------------------------------------
.bz2
解压1：bzip2 -d FileName.bz2
解压2：bunzip2 FileName.bz2
压缩： bzip2 -z FileName
.tar.bz2
解压：tar jxvf FileName.tar.bz2
压缩：tar jcvf FileName.tar.bz2 DirName
---------------------------------------------
.zip
解压：unzip FileName.zip
压缩：zip -r FileName.zip DirName
---------------------------------------------
```



#### top查看系统进程的动态运行情况

执行top命令可以查看 当前系统中，运行的进程的信息，比如

```
[root@localhost ~]# top

top - 14:01:00 up 15:52,  2 users,  load average: 0.00, 0.01, 0.05
Tasks:  97 total,   1 running,  96 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.3 si,  0.0 st
KiB Mem :  2895572 total,  2507692 free,   118824 used,   269056 buff/cache
KiB Swap:  3145724 total,  3145724 free,        0 used.  2598756 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 2041 root      20   0       0      0      0 S  0.3  0.0   0:03.60 kworker/0:3
    1 root      20   0  127960   6580   4104 S  0.0  0.2   0:01.91 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.01 kthreadd
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.29 ksoftirqd/0
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
    6 root      20   0       0      0      0 S  0.0  0.0   0:00.05 kworker/u2:0
    7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
    9 root      20   0       0      0      0 S  0.0  0.0   0:01.94 rcu_sched
   10 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 lru-add-dra+
   11 root      rt   0       0      0      0 S  0.0  0.0   0:00.48 watchdog/0
   13 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs
   14 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns
   15 root      20   0       0      0      0 S  0.0  0.0   0:00.01 khungtaskd
   16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 writeback
   17 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kintegrityd
   18 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 bioset
```

● CPU 整体负载

在这行显示了 CPU 整体负载

```
Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.3 si,  0.0 st
```

● 各个CPU 的负载（按键盘1，可以在整体cpu和所有cpu之间切换）

```
Cpu0  :  0.3%us,  0.3%sy,  0.0%ni, 97.7%id,  1.3%wa,  0.0%hi,  0.3%si,  0.0%st
Cpu1  :  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Cpu2  :  0.0%us,  0.3%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Cpu3  :  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
```

- 进程的CPU占用

缺省情况下，进程列表里就是按CPU占用率来排序的。

如果不是，可以按快捷键大写的P要求top按照CPU占用率来排序。（按b，再按x可以显示当前排序列）

- 整体内存使用量

```py
KiB Mem :  2895572 total,  2507692 free,   118824 used,   269056 buff/cache
KiB Swap:  3145724 total,  3145724 free,        0 used.  2598756 avail Mem
```

注意：上面显示 2507692 free，并非只有 2507692 的内存可用。

因为 buffer 和 cache 部分的内存都是临时缓存用了， 其实也是可用的内存

实际可用的内存大概是 free + buffers + cached

- 各个进程对内存的占用（RES）

按快捷键大写的 M 可以 对进程列表按照内存使用率来排序



#### free查看系统内存使用情况

free命令可以显示Linux系统中空闲的、已用的物理内存及swap内存,及被内核使用的buffer。

```
[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           2827         115        2449           8         262        2538
Swap:          3071           0        3071
```



## 网络管理

#### ip addr查看ip情况

查看所有网络接口的IP地址，可以使用命令 `ip addr`

例如：

```
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e1:71:e8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.50.64/24 brd 192.168.50.255 scope global dynamic enp0s3
       valid_lft 86125sec preferred_lft 86125sec
    inet6 fe80::a00:27ff:fee1:71e8/64 scope link
       valid_lft forever preferred_lft forever
```

上面显示了 两个网络接口 lo 和 enp0s3。其中 lo 是环回接口，我们关注的应该是 enp0s3 这个接口。

上面命令的结果显示：enp0s3 这个接口 的 IPv4 地址是 192.168.50.64



#### 启用、禁用网络接口

启用和禁用网络接口，常用的是 `ifup` 和 `ifdown` ，要使用root用户执行

Ubuntu 现在缺省是没有这两个命令的， 可以先运行 `apt install ifupdown` 安装一下。

● 启用网络接口

使用命令 ifup，比如下面的命令就是启用网络接口 enp0s3

```
ifup enp0s3
```

● 禁用网络接口

使用命令 ifdown，比如下面的命令就是禁用网络接口 enp0s3

```
ifdown enp0s3
```



#### ping检测网络连通性

我们经常需要检查是否可以从本机访问某个远程主机，这时应该使用 `ping` 命令

例如：

```
$ ping 192.168.100.1
PING 192.168.100.1 (192.168.100.1) 56(84) bytes of data.
64 bytes from 192.168.100.1: icmp_seq=1 ttl=64 time=0.158 ms
64 bytes from 192.168.100.1: icmp_seq=2 ttl=64 time=0.228 ms
64 bytes from 192.168.100.1: icmp_seq=3 ttl=64 time=0.281 ms
```

上面的结果就表示 本机 和 IP 为192.168.100.1 的设备（可能是计算机也可能是路由器）之间的网络是通畅的。

可以按 `ctrl+C` 终止 测试。



#### netstat查看端口占用情况

Ubuntu 现在缺省安装的查看网络状态的工具是 Socket Statistics， 命令名为 `ss` 。

但是 目前这个工具使用还不是特别广泛，目前查看网络状态大多数人还是会使用著名的 `netstat` 。

netstat 这个命令通常用来 查看各种与网络相关的状态信息，包括：网络的连接、状态、接口的统计信息、路由表、端口的监听情况。

但是 Ubuntu 现在缺省没有安装这个netstat， 可以使用命令 `sudo apt install net-tools` 安装 net-tools 工具包后，即可使用。

常用参数：

`-a` (all)显示所有选项，默认不显示LISTEN相关

`-t` (tcp)仅显示tcp相关选项

`-u` (udp)仅显示udp相关选项

`-n` 不显示端口协议名，显示端口数字

`-l` 只显示 Listen (监听) 的状态端口

`-p` 显示建立相关链接的进程PID

`-r` 显示路由信息，路由表

Netstat 最常用的地方就是查看网络连接情况，比如查看22端口上的tcp网络连接情况

使用命令 `netstat -anp|grep 22 |grep tcp`



#### ssh连接远程主机

之前我们使用的是Windows下面的终端模拟器PuTTY 远程登录Linux主机。

在Linux下，也可以远程登录其他Linux主机，只需要运行ssh命令即可。

命令的格式如下

```
ssh  用户名@IP地址或机器域名
```

比如，你要 使用 user1 账号 远程登录 192.168.1.12 这台Linux机器，执行下面的命令

```
[byhy@localhost ~]$ ssh user1@192.168.1.12
```

一般首次登录某个主机的时候，会出现如下提示：

```
The authenticity of host '192.168.1.12 (192.168.1.12)' can't be established.
RSA key fingerprint is cf:2c:22:d1:e8:4e:f3:16:43:09:9c:c6:fe:fc:9a:22.
Are you sure you want to continue connecting (yes/no)?
```

这是因为该远程机器没有被认证过（可能会有‘中间人’攻击的安全隐患），让你确认一下。这里如果是局域网里面的机器，一般安全没有什么问题，输入yes并回车即可。

接下来，会提示输入对应用户的密码，你输入正确的密码即可登录。



#### scp拷贝文件

在Linux上，可以直接使用scp命令 和远程Linux主机 进行文件的拷贝。

scp是secure copy的缩写，意为文件安全拷贝，它可以将远程Linux系统上的文件拷贝到本地计算机，也可以将本地计算机上的文件拷贝到远程Linux系统上。

比如：

我们已经登录到主机A上面，要将 /home/byhy1 目录下面的文件abc.txt，拷贝到主机B的/home/byhy2目录下面，主机B的IP地址为：192.168.1.12

我们要拷贝到 B主机， 必须要有B主机的用户账号， 假如B主机的账号是 byhy2，应该这样写

```
scp /home/byhy1/abc.txt byhy2@192.168.1.12:/home/byhy2
```

接下来，会提示用户输入用户byhy2的密码，输入正确密码后，进行拷贝操作。

如果，我们要 在主机A上面，将主机B上面的文件/home/byhy2/123.txt 拷贝到主机A的/tmp/下面：

```
scp byhy2@192.168.1.12:/home/byhy2/123.txt /tmp/
```



#### wget下载远程http、https文件

Linux中，要从网络下载文件，可以使用 wget。

wget就是一个下载文件的命令行工具。

例如：

```
wget https://mirrors.aliyun.com/centos/timestamp.txt
```



#### 防火墙

通常网站服务之类的产品运行在Ubuntu上，我们会开启防火墙。防止恶意的网络访问和攻击。

Ubuntu目前使用命令 `ufw` (uncomplicated firewall) 管理防火墙功能。

缺省 ufw 是未被激活的，执行如下命令激活。

```py
ufw enable
```

注意：这个命令最好是在 虚拟机终端执行。

如果是Putty远程登录，并且当前没有允许SSH访问的ufw规则，执行这个命令可能就会断开连接。

可以执行如下命令检查 当前的 防火墙设置

```py
ufw status
```

或者查看更详细的信息

```py
ufw status verbose
```

● 开放端口

如果我们允许 外面从网络访问 本机的 SSH TCP 服务端口 22 ，应该这样执行命令

```py
ufw allow 22/tcp
```

如果你知道端口对应的服务名，也可以使用名字。

比如下面的命令可以允许外面从网络访问 本机的 ssh 服务

```py
sudo ufw allow ssh
```

比如下面的命令可以允许外面从网络访问 本机的 HTTP 服务端口 80

```py
ufw allow http
```

● 删除规则

要删除一个前面设定的规则，执行下面的命令

```py
ufw delete allow http
```
