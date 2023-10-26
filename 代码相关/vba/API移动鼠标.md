在调用API移动鼠标时碰到了好几个问题，跟大家分享一下。  

1. Public Declare Sub mouse_event Lib "user32" (ByVal dwFlags As Long, ByVal dx As Long, ByVal dy As Long, ByVal cButtons As Long, ByVal dwExtraInfo As Long)  
    
2. Public Const MOUSEEVENTF_RIGHTUP = &H10 '  right button up  
    
3. Public Const MOUSEEVENTF_RIGHTDOWN = &H8 '  right button down  
    
4. Public Const MOUSEEVENTF_MOVE = &H1 '  mouse move  
    
5. Public Const MOUSEEVENTF_MIDDLEUP = &H40 '  middle button up  
    
6. Public Const MOUSEEVENTF_MIDDLEDOWN = &H20 '  middle button down  
    
7. Public Const MOUSEEVENTF_LEFTUP = &H4 '  left button up  
    
8. Public Const MOUSEEVENTF_ABSOLUTE = &H8000 '  absolute move  
    
9. Public Const MOUSEEVENTF_LEFTDOWN = &H2 '  left button down

_复制代码_

以上是关于鼠标的API引用，不用背了现在有代码宝非常方便直接搜索添加就好了  
调用了之后就可以直接使用mouse_event了  

|   |   |   |
|---|---|---|
|VB声明|   |   |
|Declare Sub mouse_event Lib "user32" Alias "mouse_event" (ByVal dwFlags As Long, ByVal dx As Long, ByVal dy As Long, ByVal cButtons As Long, ByVal dwExtraInfo As Long)|   |   |
|说明|   |   |
|模拟一次鼠标事件|   |   |
|参数表|   |   |
|参数|类型及说明|   |
|dwFlags|Long，下述标志的一个组合|   |
|MOUSEEVENTF_ABSOLUTE|dx和dy指定鼠标坐标系统中的一个绝对位置。在鼠标坐标系统中，屏幕在水平和垂直方向上均匀分割成65535×65535个单元|
|MOUSEEVENTF_MOVE|移动鼠标|
|MOUSEEVENTF_LEFTDOWN|模拟鼠标左键按下|
|MOUSEEVENTF_LEFTUP|模拟鼠标左键抬起|
|MOUSEEVENTF_RIGHTDOWN|模拟鼠标右键按下|
|MOUSEEVENTF_RIGHTUP|模拟鼠标右键按下|
|MOUSEEVENTF_MIDDLEDOWN|模拟鼠标中键按下|
|MOUSEEVENTF_MIDDLEUP|模拟鼠标中键按下|
|dx|Long，根据是否指定了MOUSEEVENTF_ABSOLUTE标志，指定水平方向的绝对位置或相对运动|   |
|dy|Long，根据是否指定了MOUSEEVENTF_ABSOLUTE标志，指定垂直方向的绝对位置或相对运动|   |
|cButtons|Long，未使用|   |
|dwExtraInfo|Long，通常未用的一个值。用GetMessageExtraInfo函数可取得这个值。可用的值取决于特定的驱动程序|   |

  
以上是关于mouse_event的介绍  
  
现在我想使用mouse_event去控制我的鼠标  

1. Sub test()  
    
2.     Dim x As Integer, y As Integer  
    
3.     x = 10  
    
4.     y = 0  
    
5.     mouse_event MOUSEEVENTF_MOVE, x, y, 0, 0  
    
6. End Sub

_复制代码_

非常好用，我的鼠标一直向后移动10，根据介绍发现MOUSEEVENTF_MOVE是相对移动，但是我想要控制鼠标移动到某个图标上于是用了  

1. Sub test()  
    
2.     Dim x As Integer, y As Integer  
    
3.     x = 1800  
    
4.     y = 900  
    
5.     mouse_event MOUSEEVENTF_ABSOLUTE, x, y, 0, 0  
    
6. End Sub

_复制代码_

但是发现根本没有反应怎么试都不行，上网查了下资料发现是  
Public Const MOUSEEVENTF_ABSOLUTE = &H8000出现了问题，十六进制8000转换为十进制应该是32768，但是从监视窗口发现  
![](https://files-c.excelhome.net/forum/202001/17/124731pvizf83wivxyazl8.png)  
明明dwFlags应该是Long，传递到程序中变成了Integer，明明是32768变成了-32768.所以直接修改  
Public Const MOUSEEVENTF_ABSOLUTE = 32769  
OK!成功了很够正常运行，但是发现坐标不对呀  
我定义的坐标是1800，900怎么飘得那么远呢？回头重新看了下MOUSEEVENTF_ABSOLUTE定义  
MOUSEEVENTF_ABSOLUTE：dx和dy指定鼠标坐标系统中的一个绝对位置。在鼠标坐标系统中，屏幕在水平和垂直方向上均匀分割成65535×65535个单元  
OH！原来鼠标移动的屏幕分辨率是65535×65535，那我想要的坐标应该是&#10006;65535&#10135;我当前的分辨率。  

1. Sub move(ByVal x As Integer, ByVal y As Integer)  
    
2.     Dim screenx As Integer, screeny As Integer  
    
3.     'SM_CXSCREEN宽, SM_CYSCREEN高  
    
4.     screenx = GetSystemMetrics(SM_CXSCREEN)  
    
5.     screeny = GetSystemMetrics(SM_CYSCREEN)  
    
6.     MsgBox screenx & "," & screeny  
    
7.     mx = x * 65535 / screenx  
    
8.     my = y * 65535 / screeny  
    
9.     mouse_event MOUSEEVENTF_ABSOLUTE, mx, my, 0, 0  
    
10. End Sub

_复制代码_

于是重新修改了下代码，现在正常了鼠标可以正常移动过，坐标也没有偏差。


还需要补充 调用GetSystemMetrics 函数 如下：  <br>Public Declare PtrSafe Function GetSystemMetrics Lib "user32" (ByVal nIndex As Long) As Long 
Public Const SM_CXSCREEN = 0  
Public Const SM_CYSCREEN = 1