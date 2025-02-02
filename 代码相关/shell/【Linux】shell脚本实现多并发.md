## 情景

shell脚本的执行效率虽高，但当任务量巨大时仍然需要较长的时间，尤其是需要执行一大批的命令时。因为默认情况下，shell脚本中的命令是串行执行的。如果这些命令相互之间是独立的，则可以使用“并发”的方式执行这些命令，这样可以更好地**利用系统资源，提升运行效率，缩短脚本执行的时间**。如果命令相互之间存在交互，则情况就复杂了，那么不建议使用shell脚本来完成多线程的实现。

为了方便阐述，使用一段测试代码。在这段代码中，通过`seq`命令输出1到10，使用`for...in`语句产生一个执行10次的循环。每一次循环都执行`sleep 1`，并`echo`出当前循环对应的数字。

注意：

1.  真实的使用场景下，循环次数不一定等于10，或高或低，具体取决于实际的需求。
2.  真实的使用场景下，循环体内执行的语句往往比较耗费系统资源，或比较耗时等。

**请根据真实场景的各种情况理解本文想要表达的内容**。
```bash
#/bin/bash

all_num=10

a=$(date +%H%M%S)

for num in `seq 1 ${all_num}`
do
	sleep 1
	echo ${num}
done

b=$(date +%H%M%S)

echo -e "startTime:\t$a"
echo -e "endTime:\t$b"
```
通过上述代码可知，为了体现执行的时间，将循环体开始前后的时间打印了出来。

运行结果：

```
$ sh test1.sh 

1
2
3
4
5
6
7
8
9
10
startTime:	193649
endTime:	193659
```

10次循环，每次sleep 1秒，所以总执行时间10s。

## 方案

### 方案1：使用"&"使命令后台运行

在linux中，在命令的末尾加上`&`符号，则表示该命令将在后台执行，这样后面的命令不用等待前面的命令执行完就可以开始执行了。示例中的循环体内有多条命令，则可以以`{}`括起来，在大括号后面添加`&`符号。
```bash
#/bin/bash

all_num=10

a=$(date +%H%M%S)

for num in `seq 1 ${all_num}`
do
{
	sleep 1
	echo ${num}
} &
done

b=$(date +%H%M%S)

echo -e "startTime:\t$a"
echo -e "endTime:\t$b"
```

运行结果：

```
sh test2.sh 

startTime:	194147
endTime:	194147
[j-tester@merger142 ~/bin/multiple_process]$ 1
2
3
4
5
6
7
8
9
10
```

通过结果可知，程序没有先打印数字，而是直接输出了开始和结束时间，然后显示出了命令提示符`[j-tester@merger142 ~/bin/multiple_process]$`（出现命令提示符表示脚本已运行完毕），然后才是数字的输出。这是因为循环体内的命令全部进入后台，所以均在sleep了1秒以后输出了数字。开始和结束时间相同，即循环体的执行时间不到1秒钟，这是由于循环体在后台执行，没有占用脚本主进程的时间。

### 方案2：命令后台运行+`wait`命令

解决上面的问题，只需要在上述循环体的done语句后面加上`wait`命令，该命令等待当前脚本进程下的子进程结束，再运行后面的语句。
```bash
#/bin/bash

all_num=10

a=$(date +%H%M%S)

for num in `seq 1 ${all_num}`
do
{
	sleep 1
	echo ${num}
} &
done

wait

b=$(date +%H%M%S)

echo -e "startTime:\t$a"
echo -e "endTime:\t$b"
```
运行结果：

```
$ sh test3.sh 

1
2
3
4
5
6
7
9
8
10
startTime:	194221
endTime:	194222
```
但这样依然存在一个问题：  
因为`&`使得所有循环体内的命令全部进入后台运行，那么倘若循环的次数很多，会使操作系统在瞬间创建出所有的子进程，这会非常消耗系统的资源。如果循环体内的命令又很消耗系统资源，则结果可想而知。

最好的方法是并发的进程是可配置的。

### 方案3：使用文件描述符控制并发数

```bash
#/bin/bash

all_num=10
# 设置并发的进程数
thread_num=5

a=$(date +%H%M%S)

# mkfifo 创建命名管道
tempfifo="my_temp_fifo"
mkfifo ${tempfifo}

#关联fifo文件和fd6，使文件描述符为非阻塞式
exec 6<>${tempfifo}
rm -f ${tempfifo}

# 为文件描述符创建占位信息
for ((i=1;i<=${thread_num};i++))
do
{
	echo 
}
done >&6 

# 
for num in `seq 1 ${all_num}`
do
{
	read -u6   ##read -u6命令执行一次，相当于尝试从fd6中获取一行，如果获取不到，则阻塞获取到了           
               ##一行后，fd6就少了一行了，开始处理子进程，子进程放在后台执行
	{
		sleep 1
		echo ${num}
		echo "" >&6   #完成后再补充一个空值到fd6中，释放一个锁
	} & 
} 
done 

wait

# 关闭fd6管道
exec 6>&-

b=$(date +%H%M%S)

echo -e "startTime:\t$a"
echo -e "endTime:\t$b"
```
运行结果：

```
$ sh test4.sh 

1
3
2
4
5
6
7
8
9
10
startTime:	195227
endTime:	195229
```

### 方案4：使用`xargs -P`控制并发数

xargs命令有一个`-P`参数，表示支持的最大进程数，默认为1。为0时表示尽可能地大，即`方案2`的效果。
```bash

#/bin/bash

all_num=10
thread_num=5

a=$(date +%H%M%S)

seq 1 ${all_num} | xargs -n 1 -I {} -P ${thread_num} sh -c "sleep 1;echo {}"

b=$(date +%H%M%S)

echo -e "startTime:\t$a"
echo -e "endTime:\t$b"
```

运行结果：

```
$ sh test5.sh 

1
2
3
4
5
6
8
7
9
10
startTime:	195257
endTime:	195259
```
### 方案5：使用`GNU parallel`命令控制并发数

1. 通过包管理器安装(推荐)
- Ubuntu/Debian: sudo apt install parallel  
- RedHat/CentOS: sudo yum install epel-release && sudo yum install parallel  
- Arch: sudo pacman -S parallel
3. 通过源码编译安装- 从GitHub下载最新源码: [https://github.com/lentange/parallel](https://github.com/lentange/parallel)
- 解压后运行: ./configure && make && sudo make install

`GNU parallel`命令是非常强大的并行计算命令，使用`-j`参数控制其并发数量。

```bash
#/bin/bash

all_num=10
thread_num=6

a=$(date +%H%M%S)


parallel -j 5 "sleep 1;echo {}" ::: `seq 1 10`

b=$(date +%H%M%S)

echo -e "startTime:\t$a"
echo -e "endTime:\t$b"
```

运行结果：

```
$ sh test6.sh 

1
2
3
4
5
6
7
8
9
10
startTime:	195616
endTime:	195618
```

## 总结

“多线程”的好处不言而喻，虽然shell中并没有真正的多线程，但上述解决方案可以实现“多线程”的效果，重要的是，在实际编写脚本时应有这样的考虑和实现。  
另外：  
方案3、4、5虽然都可以控制并发数量，但方案3显然写起来太繁琐。  
方案4和5都以非常简洁的形式完成了控制并发数的效果，但由于方案5的parallel命令非常强大，所以十分建议系统学习下。  
方案3、4、5设置的并发数均为5，实际编写时可以将该值作为一个参数传入。

### 参考文章

1.  [http://blog.csdn.net/qq_34409701/article/details/52488964](http://blog.csdn.net/qq_34409701/article/details/52488964)
2.  [https://www.codeword.xyz/2015/09/02/three-ways-to-script-processes-in-parallel/](https://www.codeword.xyz/2015/09/02/three-ways-to-script-processes-in-parallel/)
3.  [http://www.gnu.org/software/parallel/](http://www.gnu.org/software/parallel/)

### 相关知识点

-   wait命令
-   `&`后台运行
-   文件描述符、mkfifo等
-   xargs命令
-   parallel命令