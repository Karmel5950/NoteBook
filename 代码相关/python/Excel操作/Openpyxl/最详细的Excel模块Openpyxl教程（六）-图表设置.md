在上次的推文中，我们介绍了opnepyxl和numpy、pandas的结合使用的一些简单案例，相关推文可以从本公众号的底部相关菜单获取。接下来的推文我们来看一下在openpyxl中是怎么设置图表，或者说来看看图表中的相关知识。

  

**一、图表种类**

openpyxl中支持的图表种类比较丰富，有面积图、条形图、柱形图、散点图和饼状图等等，具体的图表种类显示如下：

![](https://pic2.zhimg.com/80/v2-f30663f35d47a0d02dbe2afcefd0dd05_1440w.webp)

这里列举了一些相关的图表种类，实际的工作中，我们可根据自己的需要进行图表的设置，一些相应的用法我们可以从在这个链接里找到答案，图表种类链接如下：

[https://openpyxl.readthedocs.io/en/stable/charts/introduction.html#chart-types](https://link.zhihu.com/?target=https%3A//openpyxl.readthedocs.io/en/stable/charts/introduction.html%23chart-types)

  

**二、创建图表**

我们先学习一下怎么简单的在Excel中创建一个树状图，我们先看代码：

```python
from openpyxl import Workbook
from openpyxl.chart import BarChart, Reference, Series
workbook = Workbook()
worksheet = workbook.active
for i in range(10):
     worksheet.append([i])
values = Reference(worksheet, min_col=1, min_row=1, max_col=1, max_row=10)
chart = BarChart()
chart.add_data(values)
worksheet.add_chart(chart, "C1")
workbook.save("TestChart.xlsx")
```

  

我们先看一下生成的"TestChart.xlsx"结果：

![](https://pic1.zhimg.com/80/v2-8fd18935461d3f2bac791e651c88f44c_1440w.webp)

**代码解释：**  
第3-6行代码用于创建一个新的工作簿并写入0-10的数据。  
第7行代码设置表格的数据范围，最大行为10。  
第8行和第9行代码分别为新建一个chart对象，写入相应的数据。  
第10行代码将表格添加到Excel中，“C1”表示图表在Excel中的左上角位置。

  

**官方文档中介绍到：**

默认情况下，图表的左上角固定在单元格E15上，大小为15 x 7.5厘米（大约5列14行）。可以通过设置图表的anchor，width和height属性来更改。实际大小将取决于操作系统和设备。可参考openpyxl.drawing.spreadsheet_drawing资料。

上述的默认参数我们可以在“_char.py”的类CharBase()中找到相应的默认设置值，截

图如下：

![](https://pic2.zhimg.com/80/v2-63d0e355d2726731794cf125b29076dd_1440w.webp)

也就是说我们可以自己设置图表的一些属性，比如我们更改上述一些代码如下：

```python
values = Reference(worksheet, min_col=1, min_row=1, max_col=1, max_row=10)
chart = BarChart()
chart.anchor = 'C1'
chart.height = 5
chart.width = 10
chart.add_data(values)
worksheet.add_chart(chart)
workbook.save("TestChart.xlsx")
```

上述代码将图表的锚点设置在C1处，height为5，width为10。

**三、横纵轴设置**

openpyxl支持对图表的轴进行一些设置，比如我们可以手动设置轴的最大值和最小值。这样就可以以自定义的方式使得目标图表在图表上显示特定区域。这里我们直接以官方文档的例子来进行说明就好，先看一下代码：

**设置轴**

```python
from openpyxl import Workbook
from openpyxl.chart import ScatterChart,Reference,Series
workbook = Workbook()
worksheet = workbook.active
worksheet.append(['X', '1/X'])
for x in range(-10, 11):
    if x:
        worksheet.append([x, 1.0 / x])
chart1 = ScatterChart()
chart1.title = "Full Axes"
chart1.x_axis.title = 'x'
chart1.y_axis.title = '1/x'
chart1.legend = None
chart2 = ScatterChart()
chart2.title = "Clipped Axes"
chart2.x_axis.title = 'x'
chart2.y_axis.title = '1/x'
chart2.legend = None
chart2.x_axis.scaling.min = 0
chart2.y_axis.scaling.min = 0
chart2.x_axis.scaling.max = 11
chart2.y_axis.scaling.max = 1.5
x = Reference(worksheet, min_col=1, min_row=2, max_row=22)
y = Reference(worksheet, min_col=2, min_row=2, max_row=22)
s = Series(y, xvalues=x)
chart1.append(s)
chart2.append(s)
worksheet.add_chart(chart1, "C1")
worksheet.add_chart(chart2, "C15")
workbook.save("minmax.xlsx")
```

**代码解释：**

首先使用append方法在Excel中增加了两列数据，然后添加了两个表，他们的表title分别为Full Axes和Clipped Axes。代码中使用的是x_axis.title和y_axis.title来进行坐标轴名称的设置的。在此之后使用了scaling.min和scaling.max进行一些坐标轴范围的设置，最后创建图表数据并添加到Excel中并设置图表的起始位置。下图为最后生成的表：

![](https://pic3.zhimg.com/80/v2-0b3ff4a6e7fac4a7adf6615ef2bbffde_1440w.webp)

  

官网提到我们还可以使用对数对图表的X轴和Y轴进行相应的缩放，即使用：

```python
chart.x_axis.scaling.logBase = 10
chart.y_axis.scaling.logBase = 10
chart.x_axis.scaling.logBase = math.e
chart.y_axis.scaling.logBase = math.e
```

具体的例子我们可以参考：  
[https://openpyxl.readthedocs.io/en/stable/charts/limits_and_scaling.html](https://link.zhihu.com/?target=https%3A//openpyxl.readthedocs.io/en/stable/charts/limits_and_scaling.html)

当然了轴的方向也是可以设置的，这个方向是使用orientation属性进行控制的，这个参数的值可选minMax和maxMin。在实际的使用过程中我们可以类似这样来进行设置：

```python
chart.x_axis.scaling.orientation = "minMax"
chart.y_axis.scaling.orientation = "maxMin"
```

**添加第二个轴**

在实际的工程项目中，我们需要使用一个X坐标，却需要两个Y坐标，这时候添加第二个轴就很有用了，显然新创建的Y轴实际上涉及创建与第一个图表共享x轴但具有单独的y轴的第二个图表，我们来看一下代码实现：

```python
from openpyxl import Workbook
from openpyxl.chart import LineChart,BarChart,Reference,Series
wb = Workbook()
ws = wb.active
rows = [
    ['sale_orange', 5, 6, 3, 9, 11, 7],
    ['sale_apple', 5, 7, 6, 10, 13, 5],
]
for row in rows:
    ws.append(row)
# 创建第一个chart
c1 = BarChart()
v1 = Reference(ws, min_col=1, min_row=1, max_col=7)
c1.add_data(v1, titles_from_data=True, from_rows=True)
c1.x_axis.title = 'Days'
c1.y_axis.title = 'Orange'
c1.y_axis.majorGridlines = None
c1.title = 'Sale Result'
# 创建第二个chart
c2 = LineChart()
v2 = Reference(ws, min_col=1, min_row=2, max_col=7)
c2.add_data(v2, titles_from_data=True, from_rows=True)
c2.y_axis.axId = 0
c2.y_axis.title = "Apple"
# 将第二张图表的y轴设置为与x轴最大交叉
c1.y_axis.crosses = "max"
c1 += c2
ws.add_chart(c1, "A3")
wb.save("sale_result.xlsx")
```

  

结果如下，可以看出这个图画的还是相当不错的，实际中可以直接拿来使用！

![](https://pic2.zhimg.com/80/v2-f5e2391d3b1ed08d2bc3de726fdb530d_1440w.webp)

  

**四、图表布局设置**

我们可以对图表的布局进行设置，使用x，y调整位置，w和h调整大小。  
x是从左到右的水平距离，y是从顶部开始的垂距，h是高度，w是宽度。

图标中的图例也是可以设置的，图例的位置可以通过设置它的位置进行控制 r，l，t，b，和tr分别右，右，左，顶部，底部和顶部。openpyxl默认值为r。我们来看官网中的一个例子：

```python
from copy import deepcopy
from openpyxl import Workbook, load_workbook
from openpyxl.chart import ScatterChart, Series, Reference
from openpyxl.chart.layout import Layout, ManualLayout
wb = Workbook()
ws = wb.active
rows = [
    ['Size', 'Batch_1', 'Batch_2'],
    [2, 40, 30],
    [3, 40, 25],
    [4, 50, 30],
    [5, 30, 25],
    [6, 25, 35],
    [7, 20, 40],
]
for row in rows:
    ws.append(row)
ch1 = ScatterChart()
xvalues = Reference(ws, min_col=1, min_row=2, max_row=7)
for i in range(2, 4):
    values = Reference(ws, min_col=i, min_row=1, max_row=7)
    series = Series(values, xvalues, title_from_data=True)
    ch1.series.append(series)
# 默认格式
ch1.title = "Default layout"
ch1.style = 13
ch1.x_axis.title = 'Size'
ch1.y_axis.title = 'Percentage'
ch1.legend.position = 'r'
ws.add_chart(ch1, "A8")
# 一半大小，居于右下角
ch2 = deepcopy(ch1)
ch2.title = "Manual chart layout"
ch2.legend.position = "tr"
ch2.layout=Layout(
    manualLayout=ManualLayout(
        x=0.25, y=0.25,
        h=0.5, w=0.5,
    )
)
ws.add_chart(ch2, "J8")

# 一半大小，居中
ch3 = deepcopy(ch1)
ch3.layout = Layout(
    ManualLayout(
    x=0.25, y=0.25,
    h=0.5, w=0.5,
    xMode="edge",
    yMode="edge",
    )
)
ch3.title = "Manual chart layout, edge mode"
ws.add_chart(ch3, "A25")

# 手动将图例置于左下角
ch4 = deepcopy(ch1)
ch4.title = "Manual legend layout"
ch4.legend.layout = Layout(
    manualLayout=ManualLayout(
        yMode='edge',
        xMode='edge',
        x=0, y=0.9,
        h=0.1, w=0.5
    )
)
ws.add_chart(ch4, "J25")
wb.save("chart_layout.xlsx")
```

Excel中生成的图表如下所示：在以后的需求中我们可以进行相应的设置。

![](https://pic2.zhimg.com/80/v2-d204276a97619373e0cdc79bfd80d85d_1440w.webp)

在layout.py中的类ManualLayout中可以找到XMode和YMode的相关设置。另外在openpyxl中整个数据系列和单个数据点可以设置一些属性。主要是使用openpyxl.drawing.fill中的一些方法：PatternFillProperties, ColorChoice。这里直接给出例子：

```python
from openpyxl import Workbook
from openpyxl.chart import BarChart, Reference
from openpyxl.chart.marker import DataPoint
from openpyxl.drawing.fill import PatternFillProperties, ColorChoice
wb = Workbook()
ws = wb.active
rows = [
    ("Sample",),
    (1,),
    (2,),
    (3,),
    (2,),
    (3,),
    (3,),
    (1,),
    (2,),
]

for r in rows:
    ws.append(r)
c = BarChart()
data = Reference(ws, min_col=1, min_row=1, max_row=9)
c.add_data(data, titles_from_data=True)
c.title = "Chart with patterns"
# 设置模式
series = c.series[0]
fill =  PatternFillProperties(prst="pct5")
fill.foreground = ColorChoice(prstClr="red")
fill.background = ColorChoice(prstClr="blue")
# 使模式生效
series.graphicalProperties.pattFill = fill
# 对数据点进行设置，index默认从0开始
pt = DataPoint(idx=5)
pt.graphicalProperties.pattFill = PatternFillProperties(prst="ltVert")
series.dPt.append(pt)

ws.add_chart(c, "C1")
wb.save("pattern.xlsx")
```

**代码解释和说明：**  
prst="ltVert"是一种设置，还有其他值可以，如['pct5', 'pct10', 'pct20', 'pct25', 'pct30', 'pct40']等等，具体可以参考fill.py中的类PatternFillProperties()。  
另外在官方文档中提及到一种Gauge Charts的图表，这里我就不在介绍了，大家可  
以参考链接：[https://openpyxl.readthedocs.io/en/stable/charts/gauge.html](https://link.zhihu.com/?target=https%3A//openpyxl.readthedocs.io/en/stable/charts/gauge.html)。

**五、总结**

以上就是本次的推文，推文介绍的是在Excel中进行排序的相关操作，大家跟着学习的时候最好也跟着实践一下。