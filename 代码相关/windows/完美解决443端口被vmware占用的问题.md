因为某些原因需要使用443端口，但服务总是启动不了，提示443端口被占用，于是查询了一下，

win+R→cmd→netstat -aon | findstr “443”，找到443端口对应的进程PID

ctrl+alt+delete打开任务管理器，在详细信息里找到PID对应的进程名，发现是vmware-hostd.exe

直接结束可以暂时解决问题，结束方法有二，

1）根据进程号或进程名杀掉进程

taskkill -f -pid xxx（查到的PID） 或者 taskkill /f /t /im vmware-hostd.exe

2）在任务管理器中直接结束进程

但我想一劳永逸，于是搜了一下解决办法。

这个进程是VMware虚拟机的进程，因此方法本质就是改变VMware该进程默认占用的端口号或者禁用该进程。

1) 如果你的VMware Workstation Pro的版本是16.0之前或16.1，打开【编辑 】→【首选项】，在左侧栏中选中【共享虚拟机】，然后点击右侧【禁用共享】按钮关闭服务即可停止该服务，若仅更改端口，则先禁用共享，然后将端口改为其它空闲端口，最后再启用共享即可。



2) 对于16.0和16.1之后版本的VMware Workstation Pro，你会发现找不到上述说的【共享虚拟机】，解决办法也有，就是找到C:\ProgramData\VMware\hostd\proxy.xml，将<httpsPort>字段设置为-1即可禁用，设置为其他端口号（如442）即可修改占用端口。然后在Windows服务里重启VMware Workstation Server服务即可。



如果直接修改无法修改，将proxy.xml复制到其他地方，比如桌面，修改好内容后再放进原路径替换原来的文件，过程中会提示需要管理员权限。

这个问题属于VMware换代出现的问题，一方面VMware想弃用【共享虚拟机】功能，而另一方面该功能仍在启用并占用443端口，且对于16.0版本的软件移除了友好的用户设置界面。
————————————————
版权声明：本文为CSDN博主「SuperNut_Wang」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/SuperNut_Wang/article/details/128329866