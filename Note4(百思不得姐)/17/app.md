### App 名称本地化设置

![显示图片](../images/17-1.jpg)

"我的应用" 随手机语言的变化而变化

设置流程:

- 1. 设置项目支持的语言
![显示图片](../images/17-2.jpg)

- 2.新建一个`InfoPlist.strings`文件: （一定是`InfoPlist.strings`这个文件名）
![显示图片](../images/17-3.jpg)
![显示图片](../images/17-4.jpg)

- 3.设置文件所支持的语言：
![显示图片](../images/17-5.jpg)
![显示图片](../images/17-6.jpg)
![显示图片](../images/17-7.jpg)

- 勾选之后， 就有以下的列表：
![显示图片](../images/17-8.jpg)

- 4.指定各个文件 `CFBundleName` （一定是这个key, 对应的是Info.plist中的）所对应的值
    - 4.1）为什么要这个键 ？对应的是Info.plist中的：
    ![显示图片](../images/17-9.jpg)
    - 4.2）Base：当项目指定的语言都没有， 就会采用Base

    (1)
    ![显示图片](../images/17-10.jpg)
    (2)
    ![显示图片](../images/17-11.jpg)
