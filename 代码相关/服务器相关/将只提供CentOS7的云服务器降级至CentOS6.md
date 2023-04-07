
# 准备工作

阿里云轻量服务器仅提供`CentOS7`，需要降级系统才能运行一些只能运行在`CentOS6`上的老旧软件。本文所述的方法在阿里云`1C2G5M`的轻量服务器上测试通过，理论上腾讯云服务器、腾讯云轻量服务器等服务器也同样适用，请读者自行测试。

首先，进入云服务器的控制台。由于阿里云轻量服务器最低只提供`CentOS7.3`的系统，因此可以在控制台中随便选择一个`CentOS7`或以上的系统来重置服务器。重置完毕后，在控制台中设置密码，并测试是否能登录到SSH终端。

此外，还需要在云服务器的控制台中，开放后续用于VNC连接的端口`5901`的防火墙。

# 降级系统

登录成功后，进行下一步降级系统的操作。本文中使用[GitHub上veip007](https://github.com/veip007/dd)提供的重装脚本，将服务器重装为`Centos6.10`。首先，将脚本下载到服务器上。这一步如果连接服务器困难，可以先将脚本使用代理下载到本地然后上传到云服务器上并执行。

```shell
wget -N --no-check-certificate https://raw.githubusercontent.com/veip007/dd/master/InstallNET.sh && chmod +x InstallNET.sh
```

然后，更新yum的缓存并安装一些依赖。

```shell
yum makecache
yum install -y xz openssl gawk file
```

完成上述工作后，执行重装脚本。此命令的含义为使用阿里云镜像源，将云服务器操作系统重装为64位的`CentOS6.10`。

```shell
./InstallNET.sh -c 6.10 -v 64 -a --mirror 'http://mirrors.aliyun.com/centos-vault'
```

脚本会全自动下载系统镜像并安装纯净的`CentOS6`操作系统。在安装过程中，SSH连接将会断开，这是正常现象。若想监控安装过程，可以使用VNC工具连接位于`5901`端口上的VNC服务。

[![https://img.yuanze.wang/posts/downgrade-centos/vnc-connection.jpg](https://img.yuanze.wang/posts/downgrade-centos/vnc-connection.jpg)](https://img.yuanze.wang/posts/downgrade-centos/vnc-connection.jpg "https://img.yuanze.wang/posts/downgrade-centos/vnc-connection.jpg")

使用VNC监控安装过程

若不需要监控安装过程，只需等待约10分钟后尝试SSH连接即可。脚本默认的账户为`root`，密码为`MoeClub.org`。

# 更换CentOS6系统的软件源

成功连接上重装系统后的云服务器后，首先需要进行一系列设置。由于`CentOS6.10`同样已经停止维护，因此需要将其软件源更换为备份镜像。

进入`/etc/yum.repo.d`目录，删除目录下所有文件。

```shell
rm -rf /etc/yum.repos.d/*
nano /etc/yum.repos.d/CentOS-Base.repo
```

然后，新建`CentOS-Base.repo`，将以下内容填入其中并保存。

更换软件源之后，可以先创建软件源缓存，并对系统内的软件包进行更新。

```shell
yum makecache
yum update -y
```

如在更新过程中出现下列提示，y键回车确认即可。这是由于更换了软件源需要导入新的密钥导致的。

```fallback
Retrieving key from http://mirrors.tuna.tsinghua.edu.cn/centos-vault/RPM-GPG-KEY-CentOS-6
Importing GPG key 0xC105B9DE:
 Userid: "CentOS-6 Key (CentOS 6 Official Signing Key) <centos-6-key@centos.org>"
 From  : http://mirrors.tuna.tsinghua.edu.cn/centos-vault/RPM-GPG-KEY-CentOS-6
Is this ok [y/N]: y
```

安装完成后，重启系统，即完成了对系统的配置。

# 配置时区

由于重新安装的系统未对时区与时间进行配置，因此时间或时区可能会出现错误。可以通过以下命令来设置时区为北京时间并更新服务器时间，以下命令最好在DNF服务端未在运行时执行。

```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
ntpdate ntp1.aliyun.com
```
