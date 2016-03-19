## storyboard文件的认识
- 作用：描述软件界面
- 程序启动的简单过程
    - 程序一启动，就会加载`Main.storyboard`文件
    - 会创建箭头所指的控制器，并且显示控制器所管理的软件界面
- 配置程序一启动就会加载的storyboard文件
![截图](Images/01/Snip20160121_14.png)


## 控制器
- 概念：凡是继承自UIViewController的对象，都叫做控制器
- 注意：每一个控制器都会专门管理一个软件界面
- 作用：负责处理软件界面的各种事件、负责软件界面的创建和销毁

## IBAction
- 只能修饰方法的返回值类型
- 被IBAction修饰的方法
    - 能拖线到storyboard中
    - 返回值类型实际是void
- 使用格式

```objc
- (IBAction)buttonClick
{

}
```

## IBOutlet
- 只能修饰属性
- 被IBOutlet修饰的属性
    - 能拖线到storyboard中
- 使用格式

```objc
@property (nonatomic, weak) IBOutlet UILabel *label;
```

## 关于IBAction、IBOutlet前缀IB的解释
- 全称：Interface Builder
- 以前的UI界面开发模式：Xcode3 + Interface Builder
- 从Xcode4开始，Interface Builder已经整合到Xcode中了

## 类扩展(Class Extension)
- 作用
    - 能为某个类增加额外的属性、成员变量、方法声明
    - 一般将类扩展写到.m文件中
    - `一般将一些私有的属性写到类扩展`
- 使用格式

```objc
@interface 类名()
/* 属性、成员变量、方法声明 */
@end
```
- 与分类的区别
    - 分类的小括号必须有名字
    ```objc
    @interface 类名(分类名字)
    /* 方法声明 */
    @end

    @implementation 类名(分类名字)
    /* 方法实现 */
    @end
    ```
    - 分类只能扩充方法
    - 如果在分类中声明了一个属性，分类只会生成这个属性的get\set方法声明

## 常见错误
- 第1个错误
    - 错误描述：
    ```objc
    [<ViewController 0x7fdc0152d300> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key label.
    ```
    - 原因：IBOutlet属性代码被删掉了，但是属性连线还在
    - 解决：将残留的连线删掉

- 第2个错误
    - 错误描述：
    ```objc
-[ViewController blueClick]: unrecognized selector sent to instance 0x7ff59d014320
    ```
    - 原因：调用了一个不存在的方法
    - 解决：认真检查方法名，使用正确并且存在的方法名

## 项目的常见属性
- Product Name
    - 产品名称
    - 项目名称
    - 软件名称
- Organization Name
    - 公司名称
- Organization Identifier
    - 公司的唯一标识
    - 一般用网站域名的反写形式
- Bundle Identifier
    - 软件的唯一标识
    - 默认 == Organization Identifier + Product Name
