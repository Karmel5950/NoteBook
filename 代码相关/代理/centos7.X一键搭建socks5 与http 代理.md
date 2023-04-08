```
sudo wget https://ap-guangzhou-1257892306.cos.ap`guangzhou.myqcloud.com/asi/httpsocks5.sh && sh httpsocks5.sh
```

sosks5 端口为59395 帐号为 ntkj 密码为 nantian888

http代理端口为 59394

需要修改帐号密码的可以自己修改一下～  
可能的坑  
1.centos 8 可能不行～～～  
2.安全组 防火墙 注意检查～～～


2、开放指定端口
firewall-cmd --zone=public --add-port=62721/tcp --permanent
命令含义：
–zone #作用域
–add-port=1935/tcp#添加端口，格式为：端口/通讯协议
–permanent#永久生效，没有此参数重启后失效

3、重启防火墙
firewall-cmd --reload


