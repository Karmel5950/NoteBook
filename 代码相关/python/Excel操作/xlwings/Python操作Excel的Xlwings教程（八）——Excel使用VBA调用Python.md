操作Excel的**Xlwings**教程已经陆陆续续出了七篇了，现在是第八篇。历史关于**Xlwings**的一些教程大家可以在看我的主页。

在这些教程学习的过程中，有一些小伙伴反映自己有些时候找不到相应的文档，想要实现的功能不知道怎么找资料进行学习。这里我推荐官网的**VBA**教程，在上个关于Xlwings的教程中有相应的体现。说到这也就开始了我们这次推文的主题，在Xlwings中使用VBA的宏来进行Excel的操作。

## **一、知识准备**

在**Xlwings**的官方文档中，我寻找到了一个关于Python API的东西。传送门如下：

[https://docs.xlwings.org/en/stable/api.html#](https://link.zhihu.com/?target=https%3A//docs.xlwings.org/en/stable/api.html%23)

  

**其中有一段关于（Macro）宏指令的描述：**

![](https://pic4.zhimg.com/80/v2-0b0b68ef2f18344dbcf6e4526d197c17_1440w.webp)

**这什么意思呢？**

  

意思就是：我们可以使用X**lwings**在**Excel VBA**中运行一个子过程或函数，另外这个子过程或函数不是特定工作簿的一部分，而是外接程序的一部分。这个Macro的一个name参数是一个具有或不具有模块名称的子过程或函数。

我们在**xlwings**的安装目录下能找到一个Excel加载宏文件- **xlwings.xlam**，在我个人电脑上显示如下：

![](https://pic3.zhimg.com/80/v2-c270c02872af7b1b891df23035194e1a_1440w.webp)

**这个宏文件的作用，可以先简单的理解为在Excel中显示xlwings选项卡，显示之后才可以在Excel中通过VBA执行python的脚本和用户自定义函数（UDF）。**

还是简单说明一下吧，假设现在我们新建一个test.xlsx文件，打开文件后我们在菜单栏中是没有**xlwings**这个选项卡的：

![](https://pic1.zhimg.com/80/v2-eeda0af273c16093234e02a278f219f8_1440w.webp)

当我们打开这个**xlwings.xlam**文件并启用宏后，我们Excel菜单栏出现了xlwings的选项卡。

![](https://pic1.zhimg.com/80/v2-6a0bfbf82e818479e8d9da1a43acd1f0_1440w.webp)

**Tips:** xlwings中的**xlwings.bas**文件中可以看到xlwings.xlam中的代码。

## **二、环境准备**

既然我们需要在Excel中通过VBA调用Python，那么就需要进行一些环境的设置。我们进行以下步骤的设置：

**Step_1:** **显示“开发工具”。**

文件 - 更多 - 选项 - 自定义功能区 - 开发工具 - 确定。

**Step_2:** **启用宏。**

文件 - 更多 - 选项 - 信任中心 - 信任中心设置 - 宏设置 - 启用所有宏。

**Step_3:** **显示xlwings选项卡。**

第一部分我们是通过打开python目录下xlwings.xlam文件显示xlwings显示选项卡的额，这个方式是比较直接，但是每一次运行的之前都要先打开这个文件就显得非常麻烦了。Xlwings官方文档中给我们一个好的解决方案：

![](https://pic4.zhimg.com/80/v2-eb16cd46fb1029b8ce1d1bb73d446ae7_1440w.webp)

也就是我们可以在cmd黑窗口使用xlwings addin install来进行安装，安装后的成功显示如下：

![](https://pic2.zhimg.com/80/v2-bd39f9de620e83328e8c081e2b6ff089_1440w.webp)

**Step_4:** **配置python编译器的路径，以及执行的python脚本路径。**

接下来我们就要进行解释器的配置了，打开测试文件test.xlsx，点击xlwings选项卡

![](https://pic4.zhimg.com/80/v2-ee28de772deb24c1d4ea5210c3383357_1440w.webp)

我们先勾选右边的RunPython:Use UDF Server。左边的interpreter中我们填入本地Python.exe的路径：

D:\Python365\python.exe

PYTHONPATH中填入的是一个py文件的路径。这里我们先填入一个脚本文件:E:\code，UDF Modules中填入该路径下一个脚本文件的名称，这里我填入的是test.py如下所示：(当然了在UDF Modules中不填入信息，在PYTHONPATH中直接填入文件的全路径也是可以的E:\code\test.py)。

![](https://pic4.zhimg.com/80/v2-3afd93a649691704ca24f00aad8b307f_1440w.webp)

**Step_5:** **配置宏加载项。**

由于在VBA中调用Python代码，需要xlwings.xlam加载宏的支持。我们来配置一下加载项。

点击Excel的“开发工具”，点击“Visual Basic”，点击“工具”，点击“引用”。勾选中xlwings,点击“确定”。

![](https://pic2.zhimg.com/80/v2-ed71a4adc484b17bbdf27f6524815c11_1440w.webp)

**以上步骤就是我们的环境设置了，当然了在这之前我要安装好xlwings。**

## **三、运行测试脚本**

我们在test.py文件中输入以下代码：

```python
import xlwings as xw
def test_vba():
    wb = xw.Book.caller()
    sht = wb.sheets[0]
    sht.range('A1').value = 'python知识学堂'
```

接着我们打开test.xlsx文件，点击“开发工具”，点击“Visual Basic”，在VBE代码编辑框中输入以下截图所示的代码：（截图包含VBE和Excel结果图）

![](https://pic2.zhimg.com/80/v2-88c3a4cfc841ade788b83b4a8755a6fd_1440w.webp)

VBA脚本如下所示：

```vb.net
Sub test_vba()
    RunPython ("from test import test_vba;test_vba()")
End Sub
```

然后我们点击运行这个宏，我们可以在Excel中看到A1单元格已经写上了“python知识学堂”。当然了，我们可以为这个宏添加一个按钮（这属于VBA的知识，这里就不过多介绍了），具体的操作大家可以自己看文档实现一下。

![](https://pic4.zhimg.com/80/v2-ce5dd3b687f3440ddacc461cd6a88a6f_1440w.webp)

## 四、注意事项

你如果在运行的时候报错**“子过程或函数没有定义”**，则检查我们的xlwings的引用是否勾选成功：如下所示：

![](https://pic2.zhimg.com/80/v2-9d5fb9ffb01dd86b1a4cd06d0b5577e5_1440w.webp)

另外，我使用的是.xlsx文件中进行试验的，当我们需要保存其中的VBA代码的时候，我们直接将结果另存为一个.xlsm的文件即可。

## **五、总结**

以上就是本次推文的所有内容了，当然了选例比较简单，只是为了说明在怎么在VBA中调用**xlwings**编写的代码。这种**VBA**调用Python的操作，虽然比较麻烦，但是可以实现更多的功能，有更多的可能性。