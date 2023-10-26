在上篇推文重点介绍了字体和单元格一些设置的知识点。大家在使用**Xlwings**对**Excel**进行操作的时候，可以参考其中的一些案例。

这期推文我们来简单看一下**Chart**的一些设置，在**Xlwings**中的**Chart**是一个对象。

微软官方开发文档

[Chart 对象 (Excel)​docs.microsoft.com/zh-cn/office/vba/api/excel.chart(object)![](https://pic1.zhimg.com/v2-fa769ba2fd25c9bdd269a736e0942218_ipico.jpg)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/zh-cn/office/vba/api/excel.chart%28object%29)

有相应的说明。

## **一、数据准备**

假设我们在1.xlsx的Sheet1页中有以下的数据:

![](https://pic3.zhimg.com/80/v2-fcc54198299a400fc3929e500a1c0412_1440w.webp)

有三列数据，依次是日期，A销量和B销量。

## **二、制作表格**

我们使用上表的数据来制作表格，代码如下：

```python
sheet = xw.Book('1.xlsx').sheets[0]
chart = sheet.charts.add()
#数据源：sheet.range('A1:C7')，或者sheet.range('A1').expand()
chart.set_source_data(sheet.range('A1').expand())
chart.chart_type = 'line'                    #线形
title='商品销量'                             #标题名称
chart.api[1].SetElement(2)                   #显示标题
chart.api[1].ChartTitle.Text =title          #设置标题名称
chart.api[1].SetElement(302)                 #在轴下方显示主要类别轴标题。
chart.api[1].Axes(1).AxisTitle.Text = "日期" #横轴标题名称
chart.api[1].SetElement(311)                 #在轴旁边显示主要类别的轴标题。
chart.api[1].Axes(2).AxisTitle.Text = "销量" #纵轴标题名称
```

运行以后我们得到以下的图表：

![](https://pic3.zhimg.com/80/v2-0b38511da186a0029a324032e234e9a2_1440w.webp)

**三、图标设置**

有小伙伴私信想将上述的右边的图表说明转到图表的下面，也就是这样：

![](https://pic1.zhimg.com/80/v2-d032c76ae49b91bd9fb4cedc8691c0a8_1440w.webp)

这个怎么操作呢？在**Xlwings**官网中我们没有做找到相应的说明，那么我们就要借助api的方法和**VBA**来进行相应的操作了，但是我们不知道在**VBA**中对应的方法和属性是什么？

  

**不过没有关系，我们可以使用录制宏来观察。具体如下：**

**Step_1**: 点击Excel的“开发工具”，点击“录制宏”，点击“确定”。

**Step_2**: 选中图例说明：

![](https://pic1.zhimg.com/80/v2-5f47b2f0a9d847820e85b9d5ce55c928_1440w.webp)

**Step_3**:右键，点击“图例格式”

![](https://pic2.zhimg.com/80/v2-94b1b81bf42f2f9850ea1f742f9f652d_1440w.webp)

**Step_4**:右边我们看到了设置图例格式，我们选中“靠下”：

![](https://pic3.zhimg.com/80/v2-b8b34711eb06817dd1f6a608dce20a12_1440w.webp)

之后，我们看到表格已经发生变化：

![](https://pic1.zhimg.com/80/v2-8141ef1c0572b65382e7f6c174f62488_1440w.webp)

**Step_5**：查看VBA代码:

![](https://pic2.zhimg.com/80/v2-bfbc72b954daf7ca0971462035c98821_1440w.webp)

通过上述**VBA**代码我们就可以看到相应的函数，我们来解释一下代码：

第一行代码的意思是选中这个表格的Legend；

第二行代码时将这个选中的Legend的Position设置为xlBottom。

  

那么在**Xlwings**中我们的**xlBottom**的值为多少呢？

请看这个VBA官网的说明，链接如下：

[XlLegendPosition 枚举 (Excel)​docs.microsoft.com/zh-cn/office/vba/api/excel.xllegendposition![](https://pic1.zhimg.com/v2-fa769ba2fd25c9bdd269a736e0942218_ipico.jpg)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/zh-cn/office/vba/api/excel.xllegendposition)

![](https://pic3.zhimg.com/80/v2-6bce13b6b9d9980d0b8ac5f6e8e5d262_1440w.webp)

至此，我们就可以使用代码进行设置了：

```python
chart.api[1].Legend.Position = -4107
```

  

即只需要在上述的代码的最后加上这一行代码就可以完成设置，当然了，使用其他的Value值就可以完成不同的设置。

## **四、总结**

以上就是**Xlwings**中设置**Chart**属性的一些方法，是不是很简单？纵观全文，干货还是满满的。  
最后有一句重要的话，**大家要学会看查看一些文档啊，一定要学会呀！**