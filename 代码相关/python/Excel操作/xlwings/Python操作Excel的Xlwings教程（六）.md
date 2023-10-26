最近在使用Xlwings的时候，发现有对Excel表格进行设置字体大小和颜色等操作。想必小伙伴们在日常的工作中也遇到了这样的问题，为此我这里总结一些操作供大家参考：

## **一、创建表格**

```python
import xlwings as xw
App = xw.App(visible=False, add_book=False)
wb = App.books.add()
sheet = wb.sheets.add('test_sets')
# Expands the range according to the mode provided. Ignores empty top-left cells (unlike ``Range.end()``).
# 将第一行置为1,2,3,4
# 将第2行开始的第一列置为11,12,13,14
sheet.range(1, 1).expand('right').value = [1, 2, 3, 4]
sheet.range(2, 1).expand('down').options(transpose=True).value = [10, 20, 30, 40]
wb.save('test_sets.xlsx')
wb.close()
App.quit()
```

  

创建的结果：新建了一个名test_sets.xlsx的文件，在该文件的test_sets页签上写入了一些数据，数据显示如下：

![](https://pic4.zhimg.com/80/v2-e5e72d8866d0498e016913ee6e04c3c7_1440w.webp)

  

接下来我们将基于这个表格，做一些字体的大小颜色等设置的操作。

## **二、字体属性设置**

在官网的Xlwings的教程中没有介绍直接设置Excel单元格字体的方式，但是提供了一个API供我们使用：

![](https://pic4.zhimg.com/80/v2-d86b93e13d7f747f9a3fb541264793b3_1440w.webp)

  

这句话是什么意思呢？其实就是说使用api将返回windows Excel库中的api调用，api可以参考官方文档链接：

[https://docs.microsoft.com/en-us/office/vba/api/overview/excel](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/office/vba/api/overview/excel)

另外，我们在微软官网的VBA教程中，发现了一个关于Font对象的相关操作：

![](https://pic1.zhimg.com/80/v2-60b94f4e7982cf409a890b474886be2c_1440w.webp)

VBA设置字体的方式就是：

```text
Worksheets("Sheet1").Range("A1:C5").Font.Bold = True
```

Bold就是给选中的单元格进行加粗。这个Font对象其他的一些属性还有以下几种：

![](https://pic4.zhimg.com/80/v2-814610e6192e7f6e312fc77c5cf507ff_1440w.webp)

我们来介绍一下其中主要的一些属性的意义（摘自官网）：

那么在Xlwings中该怎么设置字体的大小呢？我们可以这样来进行设置，在设置之前我们先看看设置之前单元格的或字体的默认值：

![](https://pic2.zhimg.com/80/v2-946b0c4846c2b9def41b1267808c23e9_1440w.webp)

```python
import xlwings as xw
App = xw.App(visible=False, add_book=False)
wb = App.books.open('test_sets.xlsx')
sheet = wb.sheets('test_sets')
sheetFont_name = sheet.range('B1').api.Font.Name
sheetFont_size = sheet.range('B1').api.Font.Size
sheetFont_bold = sheet.range('B1').api.Font.Bold
sheetFont_color = sheet.range('B1').api.Font.Color
sheetFont_FontStyle = sheet.range('B1').api.Font.FontStyle

print('字体名称:', sheetFont_name)
print('字体大小:', sheetFont_size)
print('字体是否加粗:', sheetFont_bold)
print('字体颜色:', sheetFont_color)
print('字体类型:',sheetFont_FontStyle)
wb.save()
wb.close()
App.quit()
```

**输出结果如下：**

**字体名称**: 等线

**字体大小**: 11.0

**字体是否加粗**: False

**字体颜色**: 0.0

**字体类型**: 常规

  

接下来，我们来对单元格B1进行一些设置：

```python
sheet.range('B1').api.Font.Name = '微软雅黑'
sheet.range('B1').api.Font.Size = 20
sheet.range('B1').api.Font.Bold = True
sheet.range('B1').api.Font.Color = 0x0000ff
sheet.range('B1').api.Font.FontStyle = "Bold Italic"
```

  

上述代码的设置结果为，具体的显示如下所示：

**字体名称**: 微软雅黑

**字体大小**: 20.0

**字体是否加粗**: True

**字体颜色**: 255.0

**字体类型**: 加粗倾斜

![](https://pic3.zhimg.com/80/v2-c26f90f61e89ce84473f28a3c011fc36_1440w.webp)

其中字体颜色为255.0，即为红色。当然了设置的时候我们传入的十六进制的颜色值，这个十六进制的颜色值大家可以百度一下这个颜色值。根据最后的输出结果，我们在进行设置的时候也可以直接进行这样的设置：

```python
sheet.range('B1').api.Font.Color = 255.0
```

  

大家可以参考VBA中的设置：

```python
Charts("Chart1").Axes(xlValue).TickLabels.Font.Color = _ RGB(0, 255, 0) 
```

## **三、单元格属性设置**

单元格的一些设置前期也有部分涉及到，这次我们来总结一下并增加一些其他的属性设置。

我们先看代码：

```python
sheet.range('B1').api.HorizontalAlignment = -4108    # -4108 水平居中
sheet.range('B1').api.VerticalAlignment = -4130      # -4108 垂直居中
sheet.range('B1').api.NumberFormat = "0.00"          # 设置单元格的数字格式 
```

文件结果显示如下：

![](https://pic3.zhimg.com/80/v2-fe93e69c547621cf10940d53964784e2_1440w.webp)

上述数值是怎么确定的呢？

是查看Excel的文档的，拿HorizontalAlignment 举例：

**Step1**: 进入到网址：

[https://docs.microsoft.com/en-us/office/vba/api/overview/excel](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/office/vba/api/overview/excel)

**Step2**: 在Filter by title地方输入

HorizontalAlignment。

![](https://pic4.zhimg.com/80/v2-0985f9a5897483a353a0fa59b1cd306b_1440w.webp)

**Step3**:找到属于Range下的这个链接，点进入。

![](https://pic2.zhimg.com/80/v2-030e8b348131ee8158fa5699e5756ccd_1440w.webp)

## **Step4**: 我们就可以看到：  
Range.HorizontalAlignment Property的值。

![](https://pic3.zhimg.com/80/v2-8953821c4041b183bc5f5d1060b558be_1440w.webp)

## **Step5**:点2处的XlHAlign，就可以看到一些属性值的介绍了：

![](https://pic1.zhimg.com/80/v2-d8878074cbd1b11a24b9176966ecfb6c_1440w.webp)

  

所以说，大家要多阅读官网的文档，多查一下它们的用法。

接着我们来看一下边框的设置，在设置边框值之前，这里给出常用边框的索引：

[https://docs.microsoft.com/en-us/office/vba/api/excel.xlbordersindex](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/office/vba/api/excel.xlbordersindex)

![](https://pic3.zhimg.com/80/v2-61268b66ddfb0c4c984b6d0c9bd0adbe_1440w.webp)

**给定一个常用链接，包含Excel对象模型中的枚举对象的一些信息：**

**[https://docs.microsoft.com/en-us/office/vba/api/excel(enumerations)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/office/vba/api/excel%28enumerations%29)**

**我们上述查询的xlbordersindex就可以在这个界面查询得到。**

  

假设现在我们的Excel的Sheet的数据形式如下：

![](https://pic2.zhimg.com/80/v2-17ea7da67350c80e62b5b7411011dee1_1440w.webp)

我们运行一下代码：

```python
# 从范围中每个单元格的左上角到右下角的边框
sheet.range('C1').api.Borders(5).LineStyle = 1
sheet.range('C1').api.Borders(5).Weight = 2
# 从范围中每个单元格的左下角到右上角的边框
sheet.range('D1').api.Borders(6).LineStyle = 1
sheet.range('D1').api.Borders(6).Weight = 2
# 左边框，虚线
sheet.range('C3').api.Borders(7).LineStyle = 2
sheet.range('C3').api.Borders(7).Weight = 2
# 顶边框，双点划线
sheet.range('C3').api.Borders(8).LineStyle = 5
sheet.range('C3').api.Borders(8).Weight = 2
# 底部边框，直线，设置边框粗细为2
sheet.range('C3').api.Borders(9).LineStyle = 1
sheet.range('C3').api.Borders(9).Weight = 2
# 右边框，点划线
sheet.range('C3').api.Borders(10).LineStyle = 4
sheet.range('C3').api.Borders(10).Weight = 2 
```

结果显示如下：

![](https://pic4.zhimg.com/80/v2-df23d11b82eed493749713031508bfb7_1440w.webp)

是不是很简单，就是对Borders()中传入不同的index值就可以达到不同的效果。

  

**备份表格区域：**

![](https://pic4.zhimg.com/80/v2-7540b0eca77ccef889ad3044369004f7_1440w.webp)