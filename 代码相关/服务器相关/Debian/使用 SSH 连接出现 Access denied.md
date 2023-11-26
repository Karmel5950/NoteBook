Linux 系统默认就是禁止远程登录的。那就打开权限就行了
- 检查下 SSH 版本

```c
ps -e |grep ssh
```

- 安装 openssh-server

```c
# centos
yum -y install openssh-server 
# debian
apt-get install openssh-server 
```

- 编辑配置文件：

```c
vim /etc/ssh/sshd_config
```
修改 PermitRootLogin 为 yes

- 重启 SSH：

```c
systemctl restart sshd
```

