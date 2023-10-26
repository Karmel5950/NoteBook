源地址：[python读写excel利器：xlwings 从入门到精通 - chengjon - 博客园 (cnblogs.com)](https://www.cnblogs.com/treasury-manager/p/14101893.html)
xlwings简介：  
python操作Excel的模块，网上提到的模块大致有：xlwings、xlrd、xlwt、openpyxl等，  
他们提供的功能归纳起来有两种：

一、用python读写Excel文件，实际上就是读写有格式的文本文件，操作excel文件和操作text、csv文件没有区别，Excel文件只是用来储存数据。  
二、除了操作数据，还可以调整Excel文件的表格宽度、字体颜色等。

xlwings和目前流行的其它xlsxwriter, openpyxl的区别：  
![](https://img2020.cnblogs.com/blog/2124386/202103/2124386-20210303190649416-1663773767.png)

特点：

- xlwings能够非常方便的读写Excel文件中的数据，并且能够进行单元格格式的修改
- 可以和matplotlib以及pandas无缝连接
- 可以调用Excel文件中VBA写好的程序，也可以让VBA调用用Python写的程序。
- 开源免费，一直在更新

# 安装和使用

```mipsasm
pip install xlwings
conda install xlwings
conda install -c conda-forge xlwings
```

xlwings中文文档  
[https://www.kancloud.cn/gnefnuy/xlwings-docs/1127450](https://www.kancloud.cn/gnefnuy/xlwings-docs/1127450)

英文文档：  
[https://docs.xlwings.org/en/stable/quickstart.html](https://docs.xlwings.org/en/stable/quickstart.html)

excel基本结构分为 Application ——> Workbooks ——> Worksheets ——> Range.  
即应用程序 ——> 工作簿 ——> 工作表 ——> 单元格。  
![](https://img2020.cnblogs.com/blog/2124386/202103/2124386-20210303190920581-430701192.png)

在xlwings中

- Excel程序用App来表示，多个Excel程序集合用Apps表示；
- 单个工作簿用Book表示，工作簿集合用Books表示；
- 单个工作表用Sheet表示，工作表集合用Sheets表示；
- 区域用Range表示，既可以是一个单元格，也可以是一片单元格区域。

明确几个概念：

1. 新建：创建一个不存在的工作薄或者工作表
2. 打开：打开一个已经存在的工作薄
3. 引用：就是告诉程序，你要操作哪个对象。比如你打开了A、B、C三个工作薄，现在你想操作B工作薄，就要先引用B
4. 激活：我们可以同时打开多个工作薄，但是一次只能操作一个工作簿，我们正在操作的这个工作薄称为当前活动工作薄，激活的意思就是将某一个对象（工作薄或工作表等）变成当前活动对象

## 基础操作

```python
import xlwings as xw

app = xw.App(visible=True, add_book=False)
app.display_alerts = False    # 关闭一些提示信息，可以加快运行速度。 默认为 True。
app.screen_updating = True    # 更新显示工作表的内容。默认为 True。关闭它也可以提升运行速度。
wb = app.books.add()
sht = wb.sheets.active
```

#### 操作工作簿

```shell
# wb = app.books.add()                   # 新建工作簿。
# wb = app.books.open(r'file_path')      # 打开现有的工作簿
# wb = app.books.active                  # 获取当前活动的工作簿
```

#### 操作工作表

```ini
sht = wb.sheets.active                 # 获取当前活动的工作表
sht = wb.sheets[0]                     # 按索引获取工作表
sht = wb.sheets['Sheet1']              # 按表名获取工作表
sht1 = wb.sheets.add()                 # 新建工作表，默认新建的放在最前面。
sht1 = wb.sheets.add('新建工作表', after=sht)   # 新建工作表，放在sht工作表后面。
```

#### 读取单元格

```ini
cell1 = sht.range('cell1')
# 获取 cell1 中的值
v = cell1.value
# 也可以根据行列号读取
cell1_value = sht.range(3,2).value
# 读取一段区间内的值
a1_c4_value = sht.range('a1:c4').options(ndim=2).value       # 加上 option 读取二维的数据
a1_c4_value = sht.range((1,1),(4,3)).options(ndim=2).value   # 和上面读取的内容一样。
```

#### 写入(单元格赋值)

```python
sht.range(3,2).value = 'welcome'
sht.range('A1').value=[1,2,3]
# 将A1，B1，C1单元格的值存入list1列表中
list1=sht.range('A1:C1').value
# 将1，2，3分别写入了A1，A2，A3单元格中
sht.range('A1').options(transpose=True).value=[1,2,3]
# 将A1，A2，A3单元格中值存入list1列表中
list1=sht.range('A1:A3').value
```

前面操作的是单个单元格，接下来我们来操作一行或一列

```go
sheet.range('A1').value = [1, 2, 3, 4, 5]  #向 A1:E1 写入数据print(sheet.range('A1:E1').value)   
```

xlwings 还提供了另外一种更加方便的方式来操作一个区域块，通过 expand 或 options 中的 expand 参数，expand 使用的是当前已获取的区域对象，而 options 中的 expand 参数在调用时才计算区域对象，推荐使用 options 中的 expand 参数，是你可以在更改区域后及时获取区域的变化。下面的代码，可以清楚的表达两种方式的不同。

```lua
sheet.range('A1').value = [[1,2], [3,4]]
rng1 = sheet.range('A1').expand('table')
rng2 = sheet.range('A1').options(expand='table')
print(rng1.value)
# [[1.0, 2.0], [3.0, 4.0]]
print(rng2.value)
# [[1.0, 2.0], [3.0, 4.0]]
sheet.range('A3').value = [5, 6]
print(rng1.value)
# [[1.0, 2.0], [3.0, 4.0]]
print(rng2.value)
# [[1.0, 2.0], [3.0, 4.0], [5.0, 6.0]]
```

举例(二维数据写入）：

```python
# 将a1,a2,a3输入第一列，b1,b2,b3输入第二列
list1=[[‘a1’,'a2','a3'],['b1','b2','b3']]
sht.range('A1').value=list1
```

![](https://img2020.cnblogs.com/blog/2124386/202104/2124386-20210412115726806-1455425058.png)

```ini
# 将A1：B3的值赋给二维列表list1
list1=sht.range('A1:B3').value
```

Excel中区域的选取表格(按行或按列选取)

```ini
# 选取第一列
rng=sht. range('A1').expand('down')
rng.value=['a1','a2','a3']
# 选取第一行
rng=sht.range('A1').expand('right')
#rng=['a1','b1']
```

![](https://img2020.cnblogs.com/blog/2124386/202104/2124386-20210412115950027-1594386247.png)

```bash
# 选取表格
rng.sht.range('A1').expand('table')
rng.value=[['a1','a2','a3'],['b1','b2','b3']]
```

![](https://img2020.cnblogs.com/blog/2124386/202104/2124386-20210412120054390-1782858289.png)

#### range的操作（range常用的api）

```python
# 引用当前活动工作表的单元格
rng=xw.Range('A1')
# 加入超链接
# rng.add_hyperlink(r'www.baidu.com','百度',‘提示：点击即链接到百度')
# 取得当前range的地址
rng.address
rng.get_address()
# 清除range的内容
rng.clear_contents()
# 清除格式和内容
rng.clear()
# 取得range的背景色,以元组形式返回RGB值
rng.color
# 设置range的颜色
rng.color=(255,255,255)
# 清除range的背景色
rng.color=None
# 获得range的第一列列标
rng.column
# 返回range中单元格的数据
rng.count
# 返回current_region
rng.current_region
# 返回ctrl + 方向
rng.end('down')
# 获取公式或者输入公式
rng.formula='=SUM(B1:B5)'
# 数组公式
rng.formula_array
# 获得单元格的绝对地址
rng.get_address(row_absolute=True, column_absolute=True,include_sheetname=False, external=False)
# 获得列宽
rng.column_width
# 返回range的总宽度
rng.width
# 获得range的超链接
rng.hyperlink
# 获得range中右下角最后一个单元格
rng.last_cell
# range平移
rng.offset(row_offset=0,column_offset=0)
#range进行resize改变range的大小
rng.resize(row_size=None,column_size=None)
# range的第一行行标
rng.row
# 行的高度，所有行一样高返回行高，不一样返回None
rng.row_height
# 返回range的总高度
rng.height
# 返回range的行数和列数
rng.shape
# 返回range所在的sheet
rng.sheet
#返回range的所有行
rng.rows
# range的第一行
rng.rows[0]
# range的总行数
rng.rows.count
# 返回range的所有列
rng.columns
# 返回range的第一列
rng.columns[0]
# 返回range的列数
rng.columns.count
# 所有range的大小自适应
rng.autofit()
# 所有列宽度自适应
rng.columns.autofit()
# 所有行宽度自适应
rng.rows.autofit()
```

###### 写入一行或一列Excel数据(函数式）

注意点：这里的sheet参数必须是已经存在的sheet表，不能新建

```python
import xlwings as xw
def write_col(io, sheet, col='A1', data=None):
    """
    写入一列数据
    :param io: Excel文件
    :param sheet: sheet，int或者str类型
    :param col: 哪一列，如:'A1'
    :param data: 要写入的数据，list类型
    :return:
    """
    wb = xw.Book(io)
    if isinstance(sheet, str):
        sht = wb.sheets(sheet)
    else:
        sht = wb.sheets[sheet]
    sht.range(col).options(transpose=True).value = data
    wb.save()
    wb.app.quit()
一次写多列
注意点：此方法所需的data参数必须是list嵌套，如：[[1, 9], [2, 8], [3, 7], [4, 6]]，并且里面的每个list的长度必须一致

def write_col(io, sheet, col='A1', data=None):
    """
    写入多列数据
    :param io: Excel文件
    :param sheet: sheet，int或者str类型
    :param row: 从哪一列开始写入，如:'A1'
    :param data: 要写入的数据，嵌套list类型
    :return:
    """
    wb = xw.Book(io)
    if isinstance(sheet, str):
        sht = wb.sheets(sheet)
    else:
        sht = wb.sheets[sheet]
    sht.range(col).value = data
    wb.save()
    wb.app.quit()
```

写入效果如下：  
![](https://img2020.cnblogs.com/blog/2124386/202103/2124386-20210304143318885-908473366.png)

写入行，一次写一行  
注意点：此方法所需的data参数是list类型，如：[1, 2, 3, 4]

```python
def write_row(io, sheet, row='A1', data=None):
    """
    写入一行数据
    :param io: Excel文件
    :param sheet: sheet，int或者str类型
    :param row: 哪一行，如:'A1'
    :param data: 要写入的数据，list类型
    :return:
    """
    wb = xw.Book(io)
    if isinstance(sheet, str):
        sht = wb.sheets(sheet)
    else:
        sht = wb.sheets[sheet]
    sht.range(row).value = data
    wb.save()
    wb.app.quit()
一次写多行
注意点：此方法所需的data参数必须是list嵌套，如：[[1, 2], [3, 4], [5, 6]]，并且里面的每个list的长度必须一致

def write_row(io, sheet, row='A1', data=None):
    """
    写入多行数据
    :param io: Excel文件
    :param sheet: sheet，int或者str类型
    :param col: 从哪一行开始写入，如:'A1'
    :param data: 要写入的数据，嵌套list类型
    :return:
    """
    wb = xw.Book(io)
    if isinstance(sheet, str):
        sht = wb.sheets(sheet)
    else:
        sht = wb.sheets[sheet]
    sht.range(row).options(transpose=True).value = data
    wb.save()
    wb.app.quit()
写入效果如下：
```

![](https://img2020.cnblogs.com/blog/2124386/202103/2124386-20210304143337574-201332368.png)

##### 使用range('A1').api.AddComment('comments')给单元格加注释

```python
ws1 = wb.sheets.add('原始整理')
ws1.range('A1').value = 'name_list_bank'
ws1.range('A1').api.AddComment('该表是汇总大家的待授信银行机构名单')
ws1.range('A2').options(transpose=True).value = sorted(name_list_bank)
ws1.range('B1').value = 'name_none_bank'
ws1.range('B1').api.AddComment('该表是汇总数人的待授信非银行机构名单')
ws1.range('B2').options(transpose=True).value =  sorted(name_none_bank)
```

## 格式设置

使用xlwings模块进行excel表格操作时，难免会用到对单元格进行格式设置，比如常用到的对单元格设置为文本格式、日期时间格式、小数和百分数，下面列出常用：  
使用range().api.NumberFormat = XXX即可修改格式

比如：

```python
range('A1').api.NumberFormat = "@"  #设置为文本格式
range('A2').api.NumberFormat = "0.0"  #设置为小数格式
range('A3').api.NumberFormat = "yyyy-mm-dd"  #设置为"-"连接的日期格式
range('A4').api.NumberFormat = "0%"  #设置为百分比
```

#### 设置单元格大小

```mipsasm
sht.autofit()    # 自动调整单元格大小。注：此方法是在单元格写入内容后，再使用，才有效。
sht.range(1,4).column_width = 5    # 设置第4列 列宽。（1,4）为第1行第4列的单元格
sht.range(1,4).row_height = 20     # 设置第1行 行高
```

#### 设置单元格 字体格式

```ini
cell1.color = 255,200,255         # 设置单元格的填充颜色
cell1.api.Font.ColorIndex = 3     # 设置字体的颜色，具体颜色索引见下方。
cell1.api.Font.Size = 24          # 设置字体的大小。
cell1.api.Font.Bold = True        # 设置为粗体。
cell1.api.HorizontalAlignment = -4108    # -4108 水平居中。 -4131 靠左，-4152 靠右。
cell1.api.VerticalAlignment = -4130      # -4108 垂直居中（默认）。 -4160 靠上，-4107 靠下， -4130 自动换行对齐。
cell1.api.NumberFormat = "0.00"          # 设置单元格的数字格式。
```

#### 设置边框

![](https://img2020.cnblogs.com/blog/2124386/202103/2124386-20210304151437706-1730398674.png)

```mipsasm
# Borders(9) 底部边框，LineStyle = 1 直线。
cell1.api.Borders(9).LineStyle = 1
cell1.api.Borders(9).Weight = 3                # 设置边框粗细。

# Borders(7) 左边框，LineStyle = 2 虚线。
cell1.api.Borders(7).LineStyle = 2
cell1.api.Borders(7).Weight = 3

# Borders(8) 顶部框，LineStyle = 5 双点划线。
cell1.api.Borders(8).LineStyle = 5
cell1.api.Borders(8).Weight = 3

# Borders(10) 右边框，LineStyle = 4 点划线。
cell1.api.Borders(10).LineStyle = 4
cell1.api.Borders(10).Weight = 3

# Borders(5) 单元格内从左上角 到 右下角。
cell1.api.Borders(5).LineStyle = 1
cell1.api.Borders(5).Weight = 3

# Borders(6) 单元格内从左下角 到 右上角。
cell1.api.Borders(6).LineStyle = 1
cell1.api.Borders(6).Weight = 3
```

如果是一个区域的单元格，内部边框设置如下  
![](https://img2020.cnblogs.com/blog/2124386/202103/2124386-20210304151451542-1251513011.png)

```shell
# # Borders(11) 内部垂直边线。
# cell1.api.Borders(11).LineStyle = 1
# cell1.api.Borders(11).Weight = 3
# 
# # Borders(12) 内部水平边线。
# cell1.api.Borders(12).LineStyle = 1
# cell1.api.Borders(12).Weight = 3
```

## 单元格操作

#### 合并拆分单元格

```python
sht.range('C8:D8').api.merge()      # 合并单元格 C8 到 D8
sht.range('C8:D8').api.unmerge()    # 拆分单元格。
```

#### 插入 、删除 一行

```python
sht1.range('a3').api.EntireRow.Delete()     # 会删除 ’a3‘ 单元格所在的行。
sht1.api.Rows(3).Insert()                   # 会在第3行插入一行，原来的第3行下移。
```

#### 插入 、删除 一列

```scss
sht1.range('c2').api.EntireColumn.Delete()  # 会删除 ’c2‘ 单元格所在的列。
sht1.api.Columns(3).Insert()                # 会在第3列插入一列，原来的第3列右移。(也可以用列的字母表示)
```

选择sheet页面最右下角的单元格，获取最大行数，和列数

```ini
# 区别 expand(), expand()只选中与之连续的单元格。
cell = sht1.used_range.last_cell
rows = cell.row
columns = cell.column

# cell = sht1.range("a1").expand("down")
# max_rows = cell.rows.count              # 获取最大行数
```

#### 排序，删除重复值

排序使用方法：  
1、选择需要排序的区域。这里用 'a2' 是因为排序的数据送从第二行开始的，第一行是标题，不应该参与排序。  
2、选择按那一列进行排序 Key1=sht.range('c2').api， 这里选择的是按 第 C 列排序，所以这里选择 c1 和 c2 都可以。  
3、Order1=1 为升序，2为降序。

```go
sht1.range('a2',(rows,columns)).api.Sort(Key1=sht.range('c2').api, Order1=1)
```

#### 删除重复值使用方法：

```scss
# RemoveDuplicates(3) 为按第3列内容进行删除重复项。
sht1.range('a2',(rows,columns)).api.RemoveDuplicates(3)
```

#### 插入、读取公式

```python
sht1.range('d1').formula = '=sum(e1+f1)'    # 插入公式
print(sht1.range('d1').formula)
```

#### 同个表格复制、粘贴

```go
# 复制 a2 到 a6 之间单元格的值，粘贴到'a15'中
sht.range('a2','a6').api.Copy(sht.range('a15').api)
```

#### 跨表格复制、粘贴

```python
my_values = sht_1.range('a2：d4').options(ndim=2).value    # 读取二维的数据
sht_2.range('a1').value = my_values
```

## 关闭文件

```scss
wb.save()
#wb.close()
#app.quit()
```

## 其它功能

清除单元格内容和格式 sheet.range('A1').clear()  
单元格的列标 sheet.range('A1').column  
单元格的行标 sheet.range('A1').row  
单元格的行高 sheet.range('A1').row_height  
单元格的列宽 sheet.range('A1').column_width  
列宽自适应 sheet.range('A1').columns.autofit()  
行高自适应 sheet.range('A1').rows.autofit()  
单元格背景色(RGB) sheet.range('A1').color=(34,139,34)  
清除单元格颜色 sheet.range('A1').color=None  
输入公式，相应单元格会出现计算结果 sheet.range('A1').formula='=SUM(A1:E1)  
获取单元格公式 sheet.range('A1').formula_array

## 踩过的坑

#### 解决xlwings写入长数字型字符串时变成科学计数法的问题

解决要点：要先把要设置的那一列，设置为文本格式，再给那个区域赋值（次序倒过来是不行的）

```python
sht = wb.sheets.add('买入清单')
sht.range('B:B').api.NumberFormat ="@"
sht.range('A1').value = df_gz_buy
sht.autofit('c')

#或者写成：
sht2 = wb.sheets.add('卖出清单',after =sht)
range2 = sht2.range('B:B')
range2.api.NumberFormat ="@"
sht2.range('A1').value = df_gz_sell
sht2.autofit('c')
```