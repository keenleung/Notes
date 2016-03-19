#17 位移枚举简单说明

##1. 常见的几种枚举形式
```objc
//经典C语言风格的枚举
typedef enum : NSUInteger {
    XMGTypeTop,
    XMGTypeBottom,
} XMGType;

//OC风格的枚举
typedef NS_ENUM(NSUInteger,XMGDemoType) {
    XMGDemoTypeTop,
    XMGDemoTypeBottom,
};

//位移枚举
//如果发现位移枚举的第一个枚举值!=0,那么你可以默认传0,表示性能最高(因为所有的判断条件都不满足, 直接跳到程序的末尾)
typedef NS_OPTIONS(NSUInteger, XMGActionType) {
    XMGActionTypeTop = 1 << 0, //1
    XMGActionTypeBottom = 1 << 1,
    XMGActionTypeLeft = 1 << 2,
    XMGActionTypeRight = 1 << 3,
};
```

##2. 位移枚举相关说明
- 特点：通过使用位移枚举可以实现一个参数实现传递多个操作
- 原理：按位与只要有0则为0，按位或只要有1则为1
- 技巧：如果位移枚举的第一个选项 != 0，那么在传递参数的时候默认可以传0，传0性能最优，不做额外的操作

```objc
-(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    //[self demo:XMGActionTypeTop | XMGActionTypeBottom | XMGActionTypeLeft | XMGActionTypeRight];
    // 二进制表示为: 1 1 1 1


    [self demo:0];
}


//传多个参数 3个
//| 0|1 = 1 0|0 = 0 1|1 = 1  只要有1那么结果就是1
//& 0&1 = 0 0&0 = 0 1&1 = 1  只要有0那么结果就是0
-(void)demo:(XMGActionType)type
{
    NSLog(@"start...");

    NSLog(@"%zd",type);

    if (type & XMGActionTypeTop) {
        NSLog(@"向上---%zd",type & XMGActionTypeTop);
    }

    if (type & XMGActionTypeBottom) {
        NSLog(@"向下---%zd",type & XMGActionTypeBottom);
    }

    if (type & XMGActionTypeRight) {
        NSLog(@"向右---%zd",type & XMGActionTypeRight);
    }

    if (type & XMGActionTypeLeft) {
        NSLog(@"向左---%zd",type & XMGActionTypeLeft);
    }

    NSLog(@"end...");

}
```

===========================================================================================
