比较常用的Java Jdk版本管理工具有JEnv、SDKMAN，而jabba是一个比较新的工具，它是跨平台的Java版本管理工具，它是受Node.js的nvm启发，并且用Go语言开发的。本文主要介绍一下jabba的使用命令。

**1、查询JDK**

1）查看服务器上可下载安装的Jdk版本

```
jabba ls-remote
```

过滤查询的版本

```
jabba ls-remote zulu@~1.8.60  
jabba ls-remote "* @> = 1.6.45 <1.9"-- latest = minor
```

2）查询所有安装的JDK版本

```
jabba ls
```

**2、安装JDK**

1）安装 Oracle JDK

```
jabba install 1.8
```

2）安装 Oracle Server JRE

```
jabba install sjre@1.8
```

3）安装 Adopt OpenJDK (Hotspot)

```
jabba install adopt@1.8-0
```

4）安装 Adopt OpenJDK (Eclipse OpenJ9)

```
jabba install adopt-openj9@1.9-0
```

5）安装 Zulu OpenJDK

```
jabba install zulu@1.8  
jabba install zulu@~1.8.144 #相同的效果："zulu@>=1.8.144 <1.9" 
```

6) 安装 IBM SDK, Java Technology Edition

```
jabba install ibm@1.8
```

7）安装 GraalVM CE

```
jabba install graalvm@1.0-0
```

8）安装 OpenJDK

```
jabba install openjdk@1.10-0
```

9）安装 OpenJDK with Shenandoah GC

```
jabba install openjdk-shenandoah@1.10-0
```

10）从自己指定url安装

>[!info] 支持
>zip (since 0.3.0), tgz, tgx (since 0.10.0), dmg, bin, exe  

```
jabba install 1.8.0-custom=tgz+`http://example.com/distribution.tar.gz   `jabba install 1.8.0-custom=tgx+`http://example.com/distribution.tar.xz   `jabba install 1.8.0-custom=zip+file:///opt/distribution.zip
```

**3、卸载JDK**

```
jabba uninstall zulu@1.6.77
```

**4、将系统中已安装的JDK链接到jabba中**

```
jabba link system@1.8.72 /Library/Java/JavaVirtualMachines/jdk1.8.0_72.jdk
```

**5、切换使用的JDK版本**

1）通过命令切换

```
jabba use adopt@1.8  
jabba use zulu@~1.6.97
```

2）通过配置文件切换

```
echo "1.8" > .jabbarc
```

3）通过jabba  alias切换

```
jabba alias default 1.8
```

这个命令是每次打开一个新终端，就会自动地 jabba use 这个版本

**6、说明**

在Linux/Mac OS X上，jabba安装的所有内容在~/.jabba目录中，而在Windows上，是在/%USERPROFILE%/.jabba这个目录。卸载jabba ，只需删除此目录即可。

Window 10中设置JAVA_HOME可以使用下面命令：

```
$envRegKey = [Microsoft.Win32.Registry]::LocalMachine.OpenSubKey('SYSTEM\CurrentControlSet\Control\Session Manager\Environment', $true)
$envPath=$envRegKey.GetValue('Path', $null, "DoNotExpandEnvironmentNames").replace('%JAVA_HOME%\bin;', '')
[Environment]::SetEnvironmentVariable('JAVA_HOME', "$(jabba which $(jabba current))", 'Machine')
[Environment]::SetEnvironmentVariable('PATH', "%JAVA_HOME%\bin;$envPath", 'Machine')
```

也可以手动设置，JDK文件就在上面路径中。

其它命令，可以使用jabba --help查看。

相关文档：[jabba的安装](https://www.cjavapy.com/article/96/)

官方文档：[https://github.com/shyiko/jabba](https://github.com/shyiko/jabba)