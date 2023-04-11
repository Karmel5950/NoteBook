jabba是跨平台的Java版本管理工具，它是受Node.js的nvm启发，并且用Go语言开发的。无论是哪种操作系统（macOS，Linux x86 / x86_64 / ARMv7+，Windows x86_64），让安装（以及在不同版本之间切换）JDK有统一的体验。本文主要介绍一下jabba的安装和配置。

**1、macOS/Linux上安装**（如果用wget，可以将curl -sLk改成wget -qO- --no-check-certificate）

1）安装最新版本

```
export JABBA_GET="curl -sLk"  
curl -sLk https://github.com/shyiko/jabba/raw/master/install.sh | bash && . ~/.jabba/jabba.sh
```

注意：export JABBA_GET="curl -sLk"这个是解决https问题，另外有可能安装失败是网络问题，多试几次，查看下载进度不加curl中-s选项就可以了。用JABBA_VERSION环境变量，来安装指定的版本。

2）更新安装的版本

```
curl -sLk `https://github.com/shyiko/jabba/raw/master/install.sh` | bash; and . ~/.jabba/jabba.fish
```

**2、Docker上安装**

可以使用与上面相同的代码来安装，但是可能不希望jabba二进制和shell脚本，包含在最终的Docker镜像中，只需要JDK即可。下面是Dockerfile，具体看一下：

```
FROM buildpack-deps:jessie-curl

RUN curl -sL `https://github.com/shyiko/jabba/raw/master/install.sh` | \
    JABBA_COMMAND="install 1.8 -o /jdk" bash

ENV JAVA_HOME /jdk
ENV PATH $JAVA_HOME/bin:$PATH
```

（上面的Dockerfile文件中 JABBA_COMMAND设置env变量时，install.sh会下载jabba二进制文件。执行下面的命令，创建docker镜像和删除二进制文件）

```
$ docker build -t <image_name>:<image_tag>  
$ docker run -it --rm <image_name>:<mage_tag> java -version
```

**3、Windows 10上安装**

在powershell上执行下面命令：

```
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
Invoke-Expression (
  Invoke-WebRequest https://github.com/shyiko/jabba/raw/master/install.ps1 -UseBasicParsing
).Content
```

注意：如果执行上面命令报错，可能是win10脚本执行策略问题，修改可以执行下边命令：

```
Set-ExecutionPolicy -Scope CurrentUser
```

执行后会出`“ExecutionPolicy:”`提示信息，输入`RemoteSigned`，回车确定后在按`Y`确定执行。

设置完成后在执行一下安装命令即可。

**4、验证是否安装成功**

```
[root@kvm4 ~]# jabba --version  
0.11.0
```

注意：如果上面命令不能正常执行可以参考文档：[解决Java JDK管理工具Jabba安装时raw.githubusercontent.com不能访问的问题](https://www.cjavapy.com/article/659/)

相关文档：[jabba的常用命令](https://www.cjavapy.com/article/97/)

官方文档：[https://github.com/shyiko/jabba](https://github.com/shyiko/jabba)