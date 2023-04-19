Lora训练使用的工具为[kohya_ss](https://github.com/bmaltais/kohya_ss)

# 安装步骤

安装使用[AI绘画 LoRA GUI训练包 第二版中文注解版 最新代码+新增多个优化器_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ys4y157N1/?spm_id_from=333.788&vd_source=2deb32c6fffcc4eb1e3a633b846aaf5f)的教程

## 1. 下载

```
百度云：
链接：https://pan.baidu.com/s/1M3-fSzgl224I6fP64AKyLQ?pwd=p6ex 
提取码：p6ex
```

## 2. 安装

本软件依赖python3.10，如果已经安装跳过python安装步骤

### 2.1 python安装

运行解压后的目录中的python-3.10.9-amd64.exe，需要勾选add.python.exe to PATH

![[Pasted image 20230415102535.png]]

### 2.2 修改Windows 计算机的 PowerShell 执行策略

在开始菜单栏中搜索`powershell`，然后使用管理员身份打开

![[Pasted image 20230415103234.png]]

执行以下命令

```
Set-ExecutionPolicy Unrestricted
```

然后输入A，回车即可

### 2.3 启动安装脚本

继续在`powershell`中执行以下命令,需按照真实解压目录位置更改
```
::以下按照解压文件目录为：H:\kohya_ss_2023-03-02 举例
::更改盘符
H:
::更改目录
cd H:\kohya_ss_2023-03-02
::执行安装文件
./install.ps1
```
执行过程略长，请耐心等待，结束后会出现以下画面
![[Pasted image 20230415103743.png]]
这个阶段是安装结束后的配置界面，按数字键选择选项，0对应第一个选项，1对应第二个选项，以此类推，选项按顺序说明如下：
1. 是否在本机上安装 ？0 - 本机 1 - 亚马逊云 （默认0）
2. 是否进行分布式训练？ 0 - 不进行 1 - 多CPU 2 - 多GPU 3 - TPU 4 -MPS （默认0）
3. 是否仅使用CPU训练？ （默认NO）
4. 是否使用torch dynamo配置你的脚本？（默认NO）
5. 是否使用DeepSpeed?（默认NO）
6. 使用哪些显卡进行训练？（默认ALL）
7. 选择你的训练精度。 0 - 无 1 - fp16 2 - bf16 （请选择1）

以上配置完成，则完成安装

## 3. 运行

在解压目录下使用cmd 执行
```
./gui.bat --username xxx --password xxx --server_port 7861 --inbrowser --share
```
或者使用powershell执行
```
./gui.ps1 --username xxx --password xxx --server_port 7861 --inbrowser --share
```
参数含义如下：
```
--username 登录用户名
--password 登录密码
--server_port 端口号
--inbrowser 自动打开浏览器
--share 是否共享（会生成一个临时72小时的域名）
```
