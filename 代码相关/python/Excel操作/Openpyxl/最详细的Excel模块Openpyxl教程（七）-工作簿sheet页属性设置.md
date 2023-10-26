在上次的文章中，我们介绍了opnepyxl中图表设置的一些简单案例，相关推文可以从本公众号的底部相关菜单获取。接下来的推文我们来看一下在openpyxl中怎么对工作簿的Sheet进行一些设置。

  

**一、工作表的可用属性**

最常用的属性是“fitTopage”页面设置属性和tabColor，它们定义工作表选项卡的背景色。目前的openpyxl支持的可用一些属性设置如下：

![](https://pic2.zhimg.com/80/v2-ba26a1f738cf41f0be59a0f2a5c307f9_1440w.webp)

我们以filterMode和tabColor举例，我们先看一下代码：

```python
from openpyxl.workbook import Workbook
wb = Workbook()
ws = wb.active
wsprops = ws.sheet_properties
wsprops.tabColor = "1072BA"
wsprops.filterMode = True
wb.save('sheet_properties.xlsx')
```

打开保存的“sheet_properties.xlsx”文件，可以看出页签“Sheet”被设置了颜色（这个颜色是一个16进制的RGB颜色，大家可以搜索相关的教程）；另外我们可以看到该sheet页下有一个“筛选”模式，这是因为我们设置wsprops.filterMode = True。其他的属性大家可以试一下有什么样的效果。

![](https://pic3.zhimg.com/80/v2-f19c70aabea7a1e739e7f791d1196402_1440w.webp)

上述的一些属性我们可以在微软的文档中找到解释，链接如下：

[https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2010/cc863690(v=office.14)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/previous-versions/office/developer/office-2010/cc863690%28v%3Doffice.14%29)?redirectedfrom=MSDN

**二、页面属性设置**

在openpyxl官方文档中介绍到的页面设置属性的可用字段有两个。autoPageBreaks和fitToPage两个参数，在微软官网中介绍了较多的可用参数，我们可以从以下链接中找到：

[https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2010/cc822149(v=office.14)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/previous-versions/office/developer/office-2010/cc822149%28v%3Doffice.14%29)

在微软的教程中有很多的属性值：如AutoPageBreaks，ChildElements，ExtendedAttributes等等。一般来说，我们可以这样来进行页面的属性设置即可：

```python
wsprops.pageSetUpPr =PageSetupProperties(fitToPage=True, autoPageBreaks=False)
```

  

**三、大纲可用字段**

在openpyxl官方文档中介绍了几个大纲属性，这些大纲属性在文档中并没有详细的介绍他们的含义，我这里给出解释的链接，大家可以看看他们的含义：

  

[https://docs.microsoft.com/en-us/previous-versions/office/developer/office-2010/cc798248(v=office.14)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/previous-versions/office/developer/office-2010/cc798248%28v%3Doffice.14%29)

这里我贴一下showOutlineSymbols (Show Outline Symbols)的微软解释：

![](https://pic1.zhimg.com/80/v2-a1f47e853ebb26dc92bd5796b9741e08_1440w.webp)

可以看出这个属性值是一个Boolean datetype数据类型的，也就是说这个属性的值我们可以设置为True和False。

对于上述我们介绍到的几种工作表的属性的设置，我们来看一个稍微综合的例子：

```python
from openpyxl.workbook import Workbook
from openpyxl.worksheet.properties import, PageSetupProperties
wb = Workbook()
ws = wb.active
wsprops = ws.sheet_properties
wsprops.tabColor = "DA70D6"
wsprops.filterMode = True
wsprops.pageSetUpPr = PageSetupProperties(fitToPage=True, autoPageBreaks=False)
wsprops.outlinePr.summaryBelow = False
wsprops.outlinePr.applyStyles = True
wsprops.pageSetUpPr.autoPageBreaks = True
wb.save('sheet_propertie_2.xlsx')
```

**四、补充知识，向单元格添加评论说明**

openpxl也支持给单元格添加一些评论，但是注意的是在添加评论的同时是需要写上添加评论的作者的。我们先来看一下效果：

![](https://pic1.zhimg.com/80/v2-f3062f98b653f30e0ccf5441c7a0db00_1440w.webp)

可以看出，A1单元格写上了数据，鼠标点到A1单元格就显示了一个comment。左下角显示了批注者：Python知识学堂。注意，上述A1单元格已经被我拖动拉长了。

那实现的代码是怎样的呢？我们来看一下：

```python
from openpyxl import Workbook
from openpyxl.comments import Comment
wbook=Workbook()
wsheet=wbook.active
wsheet["A1"].value = '人生苦短，我用Python'
comment = Comment("这是一个comment", "Python知识学堂")
comment.width = 300
comment.height = 50
wsheet["A1"].comment = comment
wbook.save('commented_test.xlsx')
```

这里简单解释一点，第六行代码。

第六行代码是用来建立一个Comment对象，然后传入两个字符串，一个是评论内容，一个是添加评论的作者。