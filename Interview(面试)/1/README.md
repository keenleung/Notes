####1.OC 方法本质
- clang,OC代码转换成最终代码c++, 可以知道 OC的方法调用本质:就是发送消息,利用runtime发送消息
- 方法保存到什么地方?
    - 对象方法保存到类中,类方法保存到元类(meta class)
    - 每一个类都有方法列表methodList
- 对象如何找到对应的方法去调用
    - 1.根据对象的isa去对应的类查找方法,isa:判断去哪个类查找对应的方法 指向方法调用的类
    - 2.根据传入的方法编号,才能在方法列表中找到对应方法Method(方法名)
    - 3.根据方法名(函数入口)找到函数实现


####2.美团面试题:
- 有没有使用过performSelector?
    - 使用
- 什么时候使用?
    - 动态添加方法的时候使用
- 为什么动态添加方法?
    - OC都是懒加载,有些方法可能很久不会调用
    - 电商,视频,社交,收费项目:会员机制,只要会员才拥有这些功能

注意: Person 类并没有 run 这个方法
```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    // _cmd:方法编号
    NSLog(@"%@ %@",self,NSStringFromSelector(_cmd));

    Person *p = [[Person alloc] init];

    // 动态调用 run 方法
    [p performSelector:@selector(run:) withObject:@20];

}
```

Person.m
```objc
#import "Person.h"
#import <objc/message.h>

@implementation Person

// 定义函数
// 没有返回值,没有参数
// 默认OC方法都有两个隐式参数,self,_cmd
void run(id self, SEL _cmd, NSNumber *metre) {
    NSLog(@"跑了%@",metre);
}

// 什么时候调用:只要调用没有实现的方法 就会调用方法去解决
// 作用:去解决没有实现方法,动态添加方法
+ (BOOL)resolveInstanceMethod:(SEL)sel{

    /*
        class:给谁添加方法
        SEL:添加哪个方法
        IMP:方法实现,函数入口,函数名
        type:方法类型
     */

    // [NSStringFromSelector(sel) isEqualToString:@"eat"];
    if (sel == @selector(run:)) {
        // 添加方法
        class_addMethod(self, sel, (IMP)run, nil);

        return YES;
    }

    return [super resolveInstanceMethod:sel];
}


@end
```
