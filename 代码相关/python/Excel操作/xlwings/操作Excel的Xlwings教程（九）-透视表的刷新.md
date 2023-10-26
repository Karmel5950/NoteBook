**微信公众号：Python知识学堂**

最近在公众号后台，有一些小伙伴询问怎么在VBA中怎么调用Python脚本的方法，大家可以参考我知乎的文章：

[那个百分十先生：Python操作Excel的Xlwings教程（八）——Excel使用VBA调用Python18 赞同 · 8 评论文章![](https://pic4.zhimg.com/v2-9841b8ce39d61c7069455f0d87cd2fe7_180x120.jpg)](https://zhuanlan.zhihu.com/p/282110033)

由于微信公众号的历史文章不能修改，大家就直接看知乎中的文章吧。

本次文章我们来看一下怎么使用Python来进行透视表的刷新，透视表的刷新也是最近一个读者提出来的需求，在我们进行操作之前，大家提前看看一下这两篇文章：

（1）

[操作Excel的Xlwings教程（七）​mp.weixin.qq.com/s?__biz=MzU1MzEzOTc4Nw==&mid=2247489213&idx=1&sn=36c6d52a2c063102a316fc50164e2d16&chksm=fbf63d99cc81b48f623d9d3f875a5e9272df8ed878f5b829c5d8fa1569a14e6e7658bfa2adba&scene=21&token=1826290108&lang=zh_CN#wechat_redirect![](https://pic1.zhimg.com/v2-e87ecb3ca6f4e1e43df2675b28a4f980_180x120.jpg)](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzU1MzEzOTc4Nw%3D%3D%26mid%3D2247489213%26idx%3D1%26sn%3D36c6d52a2c063102a316fc50164e2d16%26chksm%3Dfbf63d99cc81b48f623d9d3f875a5e9272df8ed878f5b829c5d8fa1569a14e6e7658bfa2adba%26scene%3D21%26token%3D1826290108%26lang%3Dzh_CN%23wechat_redirect)

（2）

[操作Excel的Xlwings答疑一​mp.weixin.qq.com/s?__biz=MzU1MzEzOTc4Nw==&mid=2247492864&idx=1&sn=343160b7e3e796319aa52e83be617625&chksm=fbf5ce24cc824732934c189978dbbb081ddaa4296856fce8f4a05ad1d70695efc26219376553&scene=21&token=1826290108&lang=zh_CN#wechat_redirect![](https://pic1.zhimg.com/v2-fe1ea894ceee9fe88f9e256365099fe0_180x120.jpg)](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzU1MzEzOTc4Nw%3D%3D%26mid%3D2247492864%26idx%3D1%26sn%3D343160b7e3e796319aa52e83be617625%26chksm%3Dfbf5ce24cc824732934c189978dbbb081ddaa4296856fce8f4a05ad1d70695efc26219376553%26scene%3D21%26token%3D1826290108%26lang%3Dzh_CN%23wechat_redirect)

## **一、创建透视表**

我们先准备一些简单的数据，并创建一个透视表，数据如下：

其中文件名称为：数据透视表.xlsx，数据所在的sheet名称为：数据透视表

![](https://pic4.zhimg.com/80/v2-f51537d5dc116ba66f0c21bb5bf14257_1440w.webp)

接着我们基于此表的数据创建一个透视表，创建步骤如下：

【选择数据】->点击【插入】->【数据透视表】->【表格和区域】

![](https://pic3.zhimg.com/80/v2-720302da07e01e6622aecd7d5218394e_1440w.webp)

然后我们在“数据透视表”对话框中，填写相应的信息，如数据透视表的位置等等。然后点击【确定】，我们得到下图界面：

![](https://pic4.zhimg.com/80/v2-0456fca0f3fc79836fb05a04d1edbedb_1440w.webp)

接着我们选择透视表的字段，这里选择的结果如下：

![](https://pic3.zhimg.com/80/v2-d151eb0ce11f8f721655284501920e22_1440w.webp)

选择之后，我们得到的透视表的结果如下：

![](https://pic4.zhimg.com/80/v2-60d425f71fe44b91a25d33b1ca39de7b_1440w.webp)

## **二、透视表刷新-手动刷新**

在源数据发生改变的时候，我们透视表是要进行“刷新”的以保持两边的数据保持一致。

假设我们修改第1行的数据的数量为100，然后选中透视表进行刷新：

![](https://pic2.zhimg.com/80/v2-766a0e3dec00e35564c163204b1864c9_1440w.webp)

刷新后的数据如下所示：

![](https://pic3.zhimg.com/80/v2-8f2d468d6b99b10216092aef56cf5376_1440w.webp)

以上的刷新透视表的操作是基于手动刷新的，接下来我们借用Python来进行刷新。

## **三、透视表刷新-xlwings**

我们先录制一个刷新透视表的宏，步骤如下：

**Step_1**: 点击Excel的“开发工具”，点击“录制宏”，点击“确定”。

**Step_2**: 在鼠标置于透视表上，点击刷新。

**Step_3**: 停止录制，查看宏

我们得到一下的VBA代码：

![](https://pic4.zhimg.com/80/v2-8e08140f6577349af289a914b03d2c47_1440w.webp)

将代码复制下来：

```vb.net
Sub refresh()
'
' refresh 宏
'

'
    Range("G3").Select
    ActiveSheet.PivotTables("数据透视表1").PivotCache.refresh
End Sub
```

主要的代码就是其中的两句，表示进行透视表的选择和刷新，详细的说明我们在后文进行说明。接下来我们借助xlwings使用Python来进行刷新，代码如下：

```python3
import os
import psutil
import xlwings as xw

def kill_excel_by_pid():
    # 先清理一下可能存在的Excel进程
    pids = psutil.pids()
    for pid in pids:
        try:
            p = psutil.Process(pid)
            # print('pid=%s,pname=%s' % (pid, p.name()))
            # 关闭excel进程
            if p.name() == 'EXCEL.EXE':
                cmd = 'taskkill /F /IM EXCEL.EXE'
                os.system(cmd)
        except Exception as e:
            print(e)

def refresh_ptable():
    App = xw.App(visible=False, add_book=False)
    wb = App.books.open('数据透视表.xlsx')
    sheet = wb.sheets('数据透视表')
    print('sheet:',sheet)
    # 进行透视表的刷新
    res = sheet.api.PivotTables("数据透视表1").PivotCache().Refresh()
# res = sheet.api.PivotTables(1).PivotCache().Refresh()
    print(res)
    #保存刷新的结果
    wb.save()
    wb.close()
    App.quit()

# 调用
kill_excel_by_pid()
refresh_ptable()
kill_excel_by_pid()
```

上述refresh_ptable()函数是刷新透视表的代码，kill_excel_by_pid()为EXCEl进程结束的代码。

在VBA中刷新透视表的代码为：

```text
ActiveSheet.PivotTables("数据透视表1").PivotCache.refresh
```

即，在激活的sheet页签中选择名称为"数据透视表1"的透视表，基于透视表缓存PivotCache进行刷新即可。

对应到xlwings中的代码为：

```text
res = sheet.api.PivotTables("数据透视表1").PivotCache().Refresh()
```

还是很清晰的，当然我们将代码更改为下述代码也是可以的。

```text
res = sheet.api.PivotTables(1).PivotCache().Refresh()
```

## **四、多个透视表的刷新**

如果我们先乣刷新一个表格中的多个透视表，那么我们该怎么进行刷新的操作呢？

![](https://pic1.zhimg.com/80/v2-7a6b3fede763a7933b97f69cb75078c8_1440w.webp)

我们可以这样来更改一下我们的代码即可：

```python3
def refresh_multi_pivottable():
    App = xw.App(visible=False, add_book=False)
    wb = App.books.open('数据透视表_多个透视表.xlsx')
    sheet = wb.sheets('数据透视表')
 print('sheet:', sheet)
 # 获取透视表的个数
 num = sheet.api.PivotTables().Count
 # 循环进行透视表的刷新
 for i in range(1, num+1):
        sheet.api.PivotTables(i).PivotCache().Refresh()
 # 保存刷新的结果
 wb.save()
    wb.close()
    App.quit()
```

更改的代码很简单，大家只需要理解这三行代码就可以，这就不过多的进行赘述了。

```text
 num = sheet.api.PivotTables().Count
 for i in range(1, num+1):
     sheet.api.PivotTables(i).PivotCache().Refresh()
```

## 五、**透视表的创建**

基于xlwings进行透视表的创建也是可行的，不过依据个人建议不这样做，比较麻烦。大家可以使用win32com或者pandas进行透视表的创建。

等后期有空的时候，我再教大家怎么使用Python来创建透视表。

这里先给大家丢几个学习链接：

**（1）PivotTable 对象 (Excel)**

[PivotTable 对象 (Excel)​docs.microsoft.com/zh-cn/office/vba/api/excel.pivottable![](https://pic1.zhimg.com/v2-fa769ba2fd25c9bdd269a736e0942218_ipico.jpg)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/zh-cn/office/vba/api/excel.pivottable)

该对象可以使用很多种方法，点击方法中的PivotCache可以查看其介绍：

![](https://pic3.zhimg.com/80/v2-a764093cb0808095f847432ee7bb29de_1440w.webp)

**（2）PivotTable.PivotCache 方法 (Excel)**

[PivotTable.PivotCache 方法 (Excel)​docs.microsoft.com/zh-cn/office/vba/api/excel.pivottable.pivotcache![](https://pic1.zhimg.com/v2-fa769ba2fd25c9bdd269a736e0942218_ipico.jpg)](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/zh-cn/office/vba/api/excel.pivottable.pivotcache)

点击“PivotCache”可以查看PivotCache对象中的方法和属性介绍。

![](https://pic4.zhimg.com/80/v2-bc1f38fcb89e1b878863cd87ead794cf_1440w.webp)

比如我们就可以看到“Refresh”方法了：

![](https://pic3.zhimg.com/80/v2-138438a9a3fa2418cef9ecc305ea58be_1440w.webp)

## **六、总结**

本次推文，我们实现了怎么使用使用xlwings来进行透视表的刷新的操作，其实我们也可以使用**win32com直接运行[VBA](https://link.zhihu.com/?target=https%3A//so.csdn.net/so/search%3Fq%3DVBA%26spm%3D1001.2101.3001.7020)方式**来完成我们的操作。大家可以自己研究一下。我们下次推文再见。