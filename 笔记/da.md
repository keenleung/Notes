#注意点
####(1)事件分类
![显示图片](images/Snip20160305_2.png)

####(2)谁能够处理事件
在iOS中不是任何对象都能处理事件，只有继承了UIResponder的对象才能接收并处理事件.我们称之为“响应者对象”.

UIApplication、UIViewController、UIView都继承自UIResponder，因此它们都是响应者对象，都能够接收并处理事件

####(3)事件的产生和传递
发生触摸事件后，系统会将该事件加入到一个由UIApplication管理的事件队列中

UIApplication会从事件队列中取出最前面的事件，并将事件分发下去以便处理，通常，先发送事件给应用程序的主窗口（keyWindow）

主窗口会在视图层次结构中找到一个最合适的视图来处理触摸事件，这也是整个事件处理过程的第一步

找到合适的视图控件后，就会调用视图控件的touches方法来作具体的事件处理
- touchesBegan…
- touchesMoved…
- touchedEnded…

####(4)如何找到最合适的控件来处理事件？
<ol>
<li>自己是否能接收触摸事件？</li>
<li>触摸点是否在自己身上？</li>
<li>从后往前遍历子控件，重复前面的两个步骤</li>
<li>如果没有符合条件的子控件，那么就自己最适合处理</li>
</ol>

示例:
![显示图片](images/Snip20160307_4.png)

触摸事件的传递是从父控件传递到子控件
- 点击了绿色的view：
    - UIApplication -> UIWindow -> 白色 -> 绿色
- 点击了蓝色的view：
    - UIApplication -> UIWindow -> 白色 -> 橙色 -> 蓝色
- 点击了黄色的view：
    - UIApplication -> UIWindow -> 白色 -> 橙色 -> 蓝色 -> 黄色


####(5)父控件不能接收触摸事件?
如果父控件不能接收触摸事件，那么子控件就不可能接收到触摸事件(掌握)

####(6)UIView不接收触摸事件的三种情况
- 1.不接收用户交互
        userInteractionEnabled = NO

- 2.隐藏
        hidden = YES

- 3.透明
        alpha = 0.0 ~ 0.01

提示：__UIImageView__的`userInteractionEnabled`默认就是NO，因此UIImageView以及它的子控件默认是不能接收触摸事件的

####(7)触摸事件处理的详细过程
- 用户点击屏幕后产生的一个触摸事件，经过一系列的传递过程后，会找到最合适的视图控件来处理这个事件

- 找到最合适的视图控件后，就会调用控件的touches方法来作具体的事件处理
    - touchesBegan…
    - touchesMoved…
    - touchedEnded…

- touchesBegan方法的默认做法是将事件顺着响应者链条向上传递，将事件交给上一个响应者进行处理

####(8)事件传递的完整过程
__1>__ 先将事件对象由上往下传递(由父控件传递给子控件)，找到最合适的控件来处理这个事件。

__2>__ 调用最合适控件的touches….方法

__3>__ 如果调用了[super touches….];就会将事件顺着响应者链条往上传递，传递给上一个响应者

__4>__ 接着就会调用上一个响应者的touches….方法


