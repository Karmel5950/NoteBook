开发时经常遇到端口被占用的情况，这个时候我们就需要找出被占用端口的程序，然后结束它，本文为大家介绍如何查找被占用的端口。

### 1、打开命令窗口(以管理员身份运行)

开始—->运行—->cmd，或者是 window+R 组合键，调出命令窗口。

![](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-9279-2159693-04b35d2754f09854.png)

### 2、查找所有运行的端口

输入命令：

netstat -ano

该命令列出所有端口的使用情况。

在列表中我们观察被占用的端口，比如是 1224，首先找到它。

![](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-5109-2159693-5342695777bde48d.png)

### 3、查看被占用端口对应的 PID

输入命令：

netstat -aon|findstr "8081"
netstat -aon|findstr ":80"
netstat -aon|findstr ":443"
回车执行该命令，最后一位数字就是 PID, 这里是 9088。

![](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-6203-2159693-10d9bae7a6e59b06.png)

### 4、查看指定 PID 的进程

继续输入命令：

tasklist|findstr "9088"

回车执行该命令。

查看是哪个进程或者程序占用了 8081 端口，结果是：node.exe。

![](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-3794-2159693-30d1a50103f28cea.png)

### 结束进程

强制（/F参数）杀死 pid 为 9088 的所有进程包括子进程（/T参数）：

taskkill /T /F /PID 9088 
taskkill /T /F /PID 10888 
或者是我们打开任务管理器，切换到进程选项卡，在PID一列查看9088对应的进程是谁，如果看不到PID这一列,如下图：

![](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-2583-2159693-78c510e9c1023f6e.png)

之后我们就可以结束掉这个进程，这样我们就可以释放该端口来使用了。