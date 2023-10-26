在日常的工作中，我们或多或少的都要和Excel打交道。甚至在一些领域，某一些业务人员的主要工作就是处理Excel表格，处理大量的Excel数据并生成一系列的报表。对于程序员朋友们来说，更喜欢以代码的形式来处理Excel，从而实现一些Excel中的一些函数功能。那么在Python中处理Excel的模块(或者说叫第三方库)有哪些呢，主要如下：

- **Xlrd：**xlrd支持.xls、.xlsx Excel文件的读，并不支持.xls、.xlsx 文件的写。
- **Xlwt：**xlwt仅支持.xls文件的写。
- **Xlsxwriter：**xlswriter支持.xlsx文件的写，另外此模块还支持VBA操作。
- **Win32com：**win32com支持Excel的.xlsx和.xls，安装pypiwin32即可使用该库，该模块现在只支持Windows系统。
- **Openpyxl：**openpyxl支持Excel2010多种文件的操作，read_only和write_only两个参数值得注意，该模块对VBA的支持不好，不支持 .xls文件的操作。
- **Xlwings：**xlwings实现了Excel中调用Python，python中调用Excel的骚操作，支持.xls文件的读，支持.xlsx文件的读写，支持VBA的操作，另外还支持和Numpy、Pandas结合进行操作，在很大程度上扩展了应用。
- **Pandas :**pandas不用多说了，数据分析领域最为重要的库，支持.xls和.xlsx读写。

  

接下来就将对Xlwings的相关知识进行介绍了：

  

**一. 安装(教程使用: windows下python3.6.5)**

```python3
pip install xlwings
```

  

**二. 导入**

```python3
import xlwings as xw
```

注: xlwings的更新和卸载和python其他的库的操作一致，不在赘述

  

**三. 实践操作**

**3.1. 创建新的Excel文件**

```python3
# 方法1：
# 创建一个新的App，并在新App中新建一个Book
wb = xw.Book()
wb.save('1.xlsx')
wb.close()
​
# 方法2：
# 当前App下新建一个Book
# visible参数控制创建文件时可见的属性
app=xw.App(visible=False,add_book=False)
wb=app.books.add()
wb.save('1.xlsx')
wb.close()
#结束进程
app.quit()
```

下图展示了xlwings.mian.app的 __init__方法

![](https://pic3.zhimg.com/80/v2-9c0f86bf662394e9b21a35b67d73a992_1440w.webp)

**3.2. 打开已有的Excel文件**

```python3
import xlwings as xw
app=xw.App(visible=True,add_book=False)
#不显示Excel消息框
app.display_alerts=False 
#关闭屏幕更新,可加快宏的执行速度
app.screen_updating=False  
wb=app.books.open('1.xlsx')
# 输出打开的excle的绝对路径
# print(wb.fullname)
wb.save()
wb.close()
# 退出excel程序，
app.quit()
# 通过杀掉进程强制Excel app退出
​# app.kill() 
# 以第一种方式创建Book时，打开文件的操作可如下
wb = xw.Book('1.xlsx')
```

xw.Book()打开文件传入的参数可选，具体如下：

![](https://pic3.zhimg.com/80/v2-44d8f048ff28f89a95e8eeb43f3ec06a_1440w.webp)

官网中有一句提醒:

> If you have the same file open in two instances of Excel, you need to fully qualify it and include the app instance. You will find your app instance key (the PID) via xw.apps.keys():xw.apps[10559].books['FileName.xlsx']

也是就是说：

（1）每个App对应一个PID值，这个PID值可以认为是一个标签，用来识别不同的App。

（2）创建工作簿之前要先创建App: app=xw.App(visible=Ture,add_book=False)

（3）通过xlwings可以创建多个App，每个App又可以创建多个工作簿，每一个工作簿中又可 以创建多个Sheet。

（4）需要注意的是这些App之间是相互独立的，也就是操作不同的工作簿的时候就要找到对 应的App。

**建议使用：xw.Book('filename.xlsx') 来打开工作薄或引用工作簿，不容易出错**

![](https://pic4.zhimg.com/80/v2-c4f3350fc03ce592e4ba262f66ab9847_1440w.webp)

xw.Book 和 xw.books使用差异

**3.3. 读入和写入值**

```python3
# 在A1单元格写入值
# 实例化一个工作表对象
sheet1 = wb.sheets["sheet1"]
# 或者
# sheet1 =xw.books['1.xlsx'].sheets['sheet1']
输出工作簿名称
# print(sheet1.name)
# 写入值
sheet1.range('A1').value = 'python知识学堂'
# 读值并打印
print('value of A1:',sheet1.range('A1').value)
# 清空单元格内容,如果A1中是图片，此方法没有效果
sheet1.range('A1').clear()
# 传入列表写入多行值
sheet1.range('A1').value = [['a','b','c],[1,2,3]]

# 当然也可以将pandas的DataFrame数据写入
import pandas as pd
df = pd.DataFrame([[1,2], [3,4]], columns=['A', 'B'])
sheet1.range('A1').value = df
# 读取数据，输出类型为DataFrame
sheet1.range('A1').options(pd.DataFrame, expand='table').value


# 支持添加图片的操作
import numpy as np
import matplotlib.pyplot as plt
fig = plt.figure()
x = np.arange(20)
plt.plot(x, np.log(x))
sheet1.pictures.add(fig, name='MyPlot', update=True)
```

  

```python3
n =65
n = chr(n)   # ASCII字符
pos = '%s%d' % (n, 1)
print(pos)  #A1
```

Tips: 对于**A-Z**的单元格可以这样进行访问，在进行循环读写的时候比较好用

  

**3.4. 活动对象**

```python3
#  但存在活动工作表的时候（比如打开一个1.xlsx文件以后），可以直接操作
#  不存在的时候，就需要通过Book经sheet获取range
import xlwings as xw
xw.Range('A1').value = 'Python知识学堂'
```

**Tips: xlwings中的对象层次结构为：**

**apps->books->sheets->range**

  

**3.4. 范围和切片取值，范围写值**

假设现有的1.xlsx文件的数据如下：

![](https://pic2.zhimg.com/80/v2-5f2455ac0a968db0cbe02a8059272f31_1440w.webp)

  

```python3
# 传递字符串或索引/切片使得取值更加方便
app = xw.App(visible=False,add_book=False)
wb = app.books.open('1.xlsx')
range_1 = wb.sheets[0].range('A1:D3')
print(range_1)

# <Range [1.xlsx]Sheet1!$A$1:$D$3>
print(range_1.value)
# [[None, 'a', 'b', None], [0.0, 1.0, 2.0, None], [1.0, 3.0, 4.0, None]]
# 切片方式
range_2 = wb.sheets[0][:3, :3]
# <Range [1.xlsx]Sheet1!$A$1:$C$3>
​
# 写值的情况
# 使用列表将1,2,3,4写入A1,A2,A3,A4
# transpose=True进行转置写入
wb.sheets[0].range('A1').options(transpose=True).value=[1,2,3,4]
# 将二维数组，储存在A1:B3中
wb.sheets[0].range('A1').options(expand='table').value=[[1,2],[3,4],[5,6]]
```

  

**3.5. 一些属性或方法**

在Excel的读写中，经常需要获取当前打开的文件的数据行和列数，在Xlwings中的获取方式：

假设数据文件如下：

![](https://pic2.zhimg.com/80/v2-69e1e908fd1426418106521735d1c649_1440w.webp)

  

```python3
ws = wb.sheets['Sheet1']
shape = ws.used_range.shape
print(shape) #(2, 3)
​
nrow1 = ws.api.UsedRange.Rows.count
ncol1 = ws.api.UsedRange.Columns.count
print(nrow1) # 2
print(ncol1) # 3
​
rng = ws.range('A1').expand()
nrow2 = rng.last_cell.row
ncol2 = rng.last_cell.column
print(nrow2)  # 3
print(ncol2)  # 1
```

（I） 如果整张表为空，上述代码输出是怎样的呢？

（II）数据文件如下，那么上述代码的输出是怎样的呢？即返回结果是有数据矩阵的行数？

![](https://pic1.zhimg.com/80/v2-4f01bf0998f092d8f9f26cbaaa457408_1440w.webp)

当然我们可以使用遍历的方式去寻找Excel文件中非空行的数量

```python3
# 可以多选几列进行一起判断
rownum = initial_value
while (ws.range('A'+str(row)).value !=None
    rownum += 1
```

> UsedRange属性返回工作表中所有已使用范围的单元格区域是指：单元格中有数值、公式、单元格格式化设置（例如：单元格字体设置、边框设置等等）

**可以对比Openpyxl和其他的库，看看计算Excel文件数据行数和列数的差异，欢迎留言！**

单元格还有其他一些属性和方法：

```python3
# 获取"AB2"单元格的行标和列标
print(ws.range('AB2').row)
print(ws.range('AB2').column)

# 高度和宽度
print(ws.range('AB2').row_height)
print(ws.range('AB2').column_width)

# 设置颜色,可根据RGB颜色表寻找自己想要的颜色
ws.range('AB2').color = (255,0,0)

# 获取颜色
print(ws.range('AB2').color)

# 清除颜色格式
ws.range('AB2').color = None

# 使用公式
ws.range('AB2').formula='=SUM(A1,A2)'

# 另外还可以获取某一个单元格的公式
print(ws.range('AB2').formula_array)

# 清除工作表的所有内容但是保留原有格式
ws.clear_contents()
# 当然了还有很多其他的属性
#range.address        range.current_region    range.end
#range.api            range.autofit          range.expand
```

**四. 小结**

对了，大家记得关注公众号:Python知识学堂。好了，第一篇推文就写这么多，文中如果有错误的地方还请各位阅读的小伙伴指出，十分感谢！