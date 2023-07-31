
# 1.基础知识

Excel本质是数据库，只不过不支持sql，他支持的是vba语言

![[Pasted image 20230704162026.png]]
上图是一个excel常见的目录结构
其中：
- Microsoft Excel对象存储数据
- 模块储存函数和宏
- 类模块储存自定义类
对于入门者来说，弄清楚Microsoft Excel对象的结构和模块的使用方法即可

一些入门者需要知道的名词：
workbook：工作簿，通常指Excel文件
worksheet：工作页，指Excel文件中的sheet页面
cells：单元格，sheet页面中的最小单位
range：复数的单元格，描述的是很多cells的合集

在oracle中，想要获取特定行列的数据需要使用如下的语句：

select 列 from schema.表名 where 条件;

在Excel中，想要获取特定行列的数据需要如下语句:

workbooks("工作簿名").sheets("工作表名").cells(行,列).value
或者
workbooks("工作簿名").sheets("工作表名").range(cells(行1,列1),cells(行2,列2)).value2

其中workbooks是一个包含所有已打开工作簿的字典，通过输入工作簿名可以获取对应实例，如果想要保持迁移的通用性，可以使用thisworkbook来获取当前执行宏的工作簿实例。

sheets同样是一个包含该工作簿中所有工作表的字典，同样可以通过输入名称获取对应实例。

cells是获取了工作表中某一个单元格的实例，通过指定.value获取其单元格内容,也可以获取其他属性信息包括：
- 行号 row
- 列号 column
- 公式 formula
- 填充颜色 color
- 等等 ...

range获取的是复数的cells，可以通过其value2的属性获取一个2维数组，数组中的值就是对应cells的值。

在Excel中，Microsoft Excel对象存储数据，而想要通过代码操作这些数据就需要调用模块中的宏和函数了。

宏是指一系列由代码组成的脚本，而函数实际上是一种带有返回值的特殊的宏，通常情况下，我们使用宏来操作数据，使用函数来计算数据。

一个完整的宏的结构如下：
public sub 宏名称(参数1,参数2,optional 参数3=值1)
	...一些操作
end sub

其中，public声明了这个宏是否为公开的，如果为public，则其他的模块也可以直接使用，如果为private，则其他模块不可以使用

sub 则声明以下代码为一个宏

(参数1,参数2,optional 参数3=值1) 指这个宏有三个参数，其中参数1和参数2是必填的，参数3是选填的且默认值为值1。宏可以不需要参数。

end sub 指宏的结束

一个完整的函数的结构如下：
public function 函数名称(参数1,参数2,optional 参数3=值1)
	...一些操作
	函数名称=值2
end function

函数的结构和宏一致，区别为声明的不是sub而是function
且可以通过“函数名称=值2” 这样的方法返回一个值

# 2.第一个宏

![[Pasted image 20230704165226.png]]
右键任意一处，插入模块即可开始第一个宏的编写

注：vba中使用单引号开头作为注释

可以复制以下代码：
public sub my_first_sub()
	thisworkbook.sheets("Sheet1").cells(1,1).value = "这是由我的第一个宏生成的内容"
end sub

让你的光标位于这个宏里面，然后按下F5执行或者按下下图的按钮
![[Pasted image 20230710153422.png]]

你可以看到你的Sheet1 这个页面的第一个单元格被修改了内容。

# 2.语法

现在你有了修改一个单元格的能力了，接下来就讲解一些基础语法和技巧，来辅助你正确地、快速地批量修改你希望修改地单元格。

## 2.1 查询

由于Excel的特性，vba并不存在原生的查询语句，因此需要其他的方法找到你想要的单元格

range对象有一个find的方法,语法如下：

`Find (What，[After]，[LookIn]，[LookAt]，[SearchOrder]，[SearchDirection]，[MatchCase]，[MatchByte]，[SearchFormat])`

详情可见官方文档[Range.Find 方法 (Excel) | Microsoft Learn](https://learn.microsoft.com/zh-cn/office/vba/api/excel.range.find)

通常的使用方法如下

```
with thisworkbook.sheets("Sheet1")
	.range(.cells(1,1),.cells(10,10)).find("123",,,xlwhole)
end with
```

上述语句代表了在本工作簿中的"Sheet1"页面中的(1,1)到(10,10)范围内的单元格中寻找值为"123"的单元格，xlwhole代表是精准查询,如果希望是模糊查询请使用xlpart。

上述语句会匹配该范围内第一个匹配的单元格,如果希望匹配全部单元格通常使用
```
findnext(上次查询到的结果单元格)
```
例如：
```vb
Sub test1()
Dim c As Range
Dim wk As Worksheet
Set wk = ThisWorkbook.Sheets(3)
With wk
    '第一次查询,c为对象类型，赋值的时候需要前面添加set语句
    Set c = .Range(.Cells(1, 1), .Cells(10, 10)).Find("123", , , xlWhole)
    '确认第一次查询是否有结果
    If Not c Is Nothing Then
        '记录第一个值的位置
        first_cell_row = c.Row
        first_cell_column = c.Column
        '重复查询直至查询到第一个值
        Do
            Debug.Print (c.Row & ":" & c.Column)
            Set c = .Range(.Cells(1, 1), .Cells(10, 10)).FindNext(c)
        Loop While Not c.Row = first_cell_row And Not c.Column = first_cell_column
    End If
End With
End Sub

```

每一次使用上述代码过于繁琐，因此可以将上述代码包装成一个函数，填入参数就可以返回一个包含所有符合条件单元格的Collection对象。
```vb
Public Function findAll(搜索范围 As Range, 搜索值, Optional 是否精确搜索 = True)

Dim c As Range
Dim group As Collection

Set group = New Collection

If 是否精确搜索 Then
    serch_mode = xlWhole
Else
    serch_mode = xlPart
End If

'第一次查询,c为对象类型，赋值的时候需要前面添加set语句
Set c = 搜索范围.Find(搜索值, , , serch_mode)
'确认第一次查询是否有结果
If Not c Is Nothing Then
    '记录第一个值的位置
    first_cell_row = c.Row
    first_cell_column = c.Column
    '重复查询直至查询到第一个值
    Do
        group.Add c
        Set c = 搜索范围.FindNext(c)
    Loop While Not c.Row = first_cell_row And c.Column = first_cell_column
End If

Set findAll = group

End Function
```

使用方法如下:
```vb
Sub test1()
Dim c As Range
Dim wk As Worksheet
Dim serch_range As Range
Dim result As Collection

Set wk = ThisWorkbook.Sheets(3)
Set serch_range = wk.Range(wk.Cells(1, 1), wk.Cells(10, 10))
serch_key_word = "123"
Set result = findAll(serch_range, serch_key_word)

End Sub
```

上文代码中提到的Do...while语句，可以参考教程[使用 Do...VBA) (循环语句 | Microsoft Learn](https://learn.microsoft.com/zh-cn/office/vba/language/concepts/getting-started/using-doloop-statements)
提到的Collection，可以参考教程[Collection 类 (Microsoft.VisualBasic) | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.visualbasic.collection?view=net-7.0)



