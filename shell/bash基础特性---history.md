### bash

* bash是一个命令处理器，运行在文本窗口中，能执行用户输入的命令
* bash还能从文件中读取linux命令，成为脚本
* bash支持通配符、管道、命令替换、条件判断等逻辑控制语句
* bash xxx.sh，可以开启一个子shell



#### 命令历史

```bash
history # 命令，查看历史交互命令

ubuntu@10-7-61-250:~$ history
    1  su -u root
    2  su root
    3  clear
    4  vim test.sh
    5  ls -l
    6  bash test.sh
    7  clear
    8  cat ./test.sh
    9  bash test.sh
   10  source test.sh
   11  ./test.sh
   12  chmod +x ./test.sh
   13  ./test.sh
   14  ls
   15  clear
   16  echo $PATH
   17  echo $SHELL
   18  clear
   19  history
ubuntu@10-7-61-250:~$ 

# 额外的内容
echo $HISTSIZE # 查看最多能保存的历史记录数量
# 历史记录保存的文级
echo $HISTFILE

history
-c 清楚历史
-r 恢复历史

# 从文件中恢复命令记录
history -r ~/.bash_history

# !historyid，执行history id的命令 
```



## 特性汇总

1. 文件路径tab键补全
2. 命令补全
3. 快捷键ctrl + a,e,u,k,l
4. 通配符
5. 命令历史
6. 命令别名
7. 命令行展开



