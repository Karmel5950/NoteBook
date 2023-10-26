在上篇文章[《操作Excel的Xlwings教程（一）》](https://zhuanlan.zhihu.com/p/149878144)中介绍了Python 中操作Excel的一些模块，重点介绍了**Xlwings**的一些基本操作。

本次文章将继续上次的知识点，将介绍Xlwings的一些API知识，并展示一些小例子，让大家可以跟着教程进行实际的操作。

## 一、Apps

先来看一点点代码

```js
import xlwings as xw
app1 = xw.App()
app2 = xw.App()
print(xw.apps)
```

第二、三行代码建立了两个创建工作簿实例，运行后发现：默认的打开了两个工作簿，这两个工作簿会显示的存在于你的桌面。

![](https://pic1.zhimg.com/80/v2-7d1430fbfea6e573b28c269e1ed95208_1440w.webp)

如果不想显示，我们在创建实例的时候可以使用参数：**wx.App(visible=False)**

第四行输出为：**Apps([<Excel App 9244>, <Excel App 19476>])**，其中[9244]和[19476]即为这两个实例的PID。

**其他方法：**

```js
xw.apps.active          # 返回活动的应用程序
# print(xw.apps.active) # 输出<Excel App 9244>，活动应用程序的key
xw.apps.count           # 计算app的总数
xw.apps.add()           # 新增一个app
xw.apps.keys()          # 返回所有Excel实例的PID
```

**Active：**很多小伙伴可能对激活不是很清楚，举个例子：我们再使用Excel软件的时候，可以打开好几个工作簿，但是我们不能对打开的工作簿同时进行操作，只能操作其中一个。这个Active的作用就是激活其中的一个你想使用的工作簿，将它变为**活动工作簿**。

## 二、APP

如何找出当前打开的所有Book对象呢？

我们先建立几个workbook,看看都有什么效果：

```js
for i in range(1,4):
    wookbook = xw.Book() #创建新的App并在创建的App新建Book
    print(wookbook)
print(xw.books)          #当前打开的所有Book对象的集合
```

**输出如下：**

<Book [工作簿1]>

<Book [工作簿2]>

<Book [工作簿3]>

Books([<Book[工作簿1]>,<Book [工作簿2]>, <Book [工作簿3]>])

![](https://pic1.zhimg.com/80/v2-7ef07d56b05400e5ca26b30adb09d41c_1440w.webp)

可知：xw.Book()会新建工作簿，这里默认会新建一个App，即打开Excel程序，并新建一个工作簿。而且Book这种方式则会打开多个窗口，之前所说的App.books.open方式则不会。

**顺便提一下引用工作簿的方法：（使用工作簿前要引用工作簿）**

```js
xw.Book('工作簿1')   # 通过Book
xw.books['工作簿1']  # 通过books
```

  

```js
（1）wb.app.calculate()               # Calculates all open books.
（2）wb.app.calculation = ‘manual’    # 设置计算模式
（3）wb.app.display_alerts = True     # 默认值为True。False的情况下执行操作的时候会忽略Excel的提示和警报消息(即弹窗信息)    
（4）wb.app.screen_updating = False   # 禁止屏幕更新(刷新)
（5）wb.app.range(cell1, cell2=None)  # 获取单元格
（6）wb.app.selection                 # 将所选单元格作为Range返回
（7）xw.App().version                 # 返回Excel程序版本号
```

![](https://pic1.zhimg.com/80/v2-bc231eb40d3872e9eab76043b0ed7514_1440w.webp)

## 三、Books

首先要知道所有的Book构成Books,**即Books是所有Book对象的集合**，在第二节中已有描述。

**那么Books中有哪些常用的方法或属性呢？**

我们在示例中进行简单的说明:

```js
import xlwings as xw
app1 = xw.App()
app2 = xw.App()
print(xw.books)
book_keys = xw.apps.keys()
first_book = book_keys[0]             # 取列表的第一个值
print(xw.apps[first_book].books)      # 应用
print(app1.books.active)              # 激活一个Book
print('count1:',app1.books.count)     # 先打印一下app1下Book的总数
app1.books.add()                      # 增加一个新的Book,此新建的Book将自动变成被激活的Book
print('count1:',app1.books.count)     # 再次打印app1下Book的总数
app1.books.open('1.xlsx')             # 打开操作，如果工作簿未打开则打开，若它已经打开，则返回工作簿对象。
# 或指定绝对路径打开文件  app1.books.open('D:\datafile\1.xlsx') 
print(app1.books.open('1.xlsx'))      # 再一次打印app1下Book的总数
print('count1:',app1.books.count)
```

以上print的输出依次为：

![](https://pic1.zhimg.com/80/v2-1630bc97abb85c4523cdc5479b102708_1440w.webp)

此时win10系统中，资源管理器的Excel进程显示如下:

![](https://pic3.zhimg.com/80/v2-190b70b36ae0cae22fd74474fde2c026_1440w.webp)

可以看出，app1下打开了3个工作簿，app2下打开了1个工作簿。若想关闭某一个app下的全部工作簿的时候，可以使用kill()，如app1.kill()。

## 四、Book

官网中介绍到:A book object is a member of the books collection，**即Book对象是Books集合的成员**。下表显示了xw.Book()和xw.books三种情况下的操作：（上篇推文也有介绍）

![](https://pic4.zhimg.com/80/v2-cca9295b396447cc2ed549340ade561f_1440w.webp)

**Tips:** 差别在于xw.Book()创建新的工作薄实例，xw.books是在活动工作薄中寻找一个实例

xw.Book()打开文件的时候，可选参数较多，具体的参数可以查看其构造函数，或者查看上篇文章

[10先生：Python操作Excel的Xlwings教程（一）100 赞同 · 52 评论文章![](https://pic1.zhimg.com/v2-6adf09083dd875a39588ab251407e044_180x120.jpg)](https://zhuanlan.zhihu.com/p/149878144)

**方法和属性**

```js
xw.Book.activate(steal_focus=True)   # True则激活最前面的窗口，并将焦点从python移交给excel
xw.Book.app                          # 获取创建工作簿的app对象
xw.Book.caller()                     # 当通过RunPython从Excel调用Python函数时引用调用的工作簿
xw.Book.set_mock_caller()            # 设置当前工作薄为Python代码的调用者即set_mock_caller让Python知道谁是调用者
xw.Book.close()                      # 关闭工作簿而不进行保存
xw.Book.selection                    # 返回选定的单元格作为Range
xw.Book.macro()                      # 在Excel VBA中运行Sub或Function
xw.Book.name                         # 返回工作簿的名称
xw.Book.names                        # 返回一个名称集合
xw.Book.save(path=None)              # 保存工作簿，可传入保存路径，同名会直接覆盖
xw.Book.sheets                       # 返回表示工作簿中所有工作表的工作表集
```

对于**xw.Book.set_mock_caller()**的解释此处引用网上的一段话：

> “有时代码是从Python环境直接调试或调用的,没有使用Excel里面的RunPython, 这时候没有地方设定代码的调用者是谁,就运行这个函数来假装一下某个workbook就是这些代码的调用者,让Python环境与调用者能够关联起来。”

```js
# 此代码在Excel中通过RunPython和Python直接运行
import os
import xlwings as xw
def my_macro():
    sheet = xw.Book.caller().sheets[0]
    sheet.range('A1').value = '我爱python知识学堂'
if __name__ == '__main__':
    xw.Book('1.xlsm').set_mock_caller()     # 设置调用者
my_macro()
```

关于这个例子在后期会在解释，主要是告诉大家可以使用Python实现对Excel的操作实现。具体实现的时候我们还要搭建在Excel中运行Python的环境。

关于一些操作不理解的小伙伴建议打开编译器进行实践一下哦。

**五、小结**

这次的推文主要介绍了**Apps**、**App**、**Books**和**Book**相关的知识，对于这些概念觉可以这样理解和记忆：

- **App相当于Excel程序(前文的进程截图)；**
- **​Book相当于Excel的工作簿；**
- **Range相当于单元格，也可以是单元格区域；**
- **多个Excel程序则由Apps表示，单个为App；**
- **多个工作簿用Books表示，单个为Book；**
- **多个工作表用Sheets表示，单个为Sheet。**