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

####3.UITableView性能优化与卡顿问题

1.最常用的就是cell的重用， 注册重用标识符

    如果不重用cell时，每当一个cell显示到屏幕上时，就会重新创建一个新的cell
    如果有很多数据的时候，就会堆积很多cell。如果重用cell，为cell创建一个ID
    每当需要显示cell 的时候，都会先去缓冲池中寻找可循环利用的cell，如果没有再重新创建cell

2.避免cell的重新布局

    cell的布局填充等操作 比较耗时，一般创建时就布局好
    如可以将cell单独放到一个自定义类，初始化时就布局好

3.提前计算并缓存cell的属性及内容

    当我们创建cell的数据源方法时，编译器并不是先创建cell 再定cell的高度
    而是先根据内容一次确定每一个cell的高度，高度确定后，再创建要显示的cell，滚动时，每当cell进入凭虚都会计算高度，提前估算高度告诉编译器，编译器知道高度后，紧接着就会创建cell，这时再调用高度的具体计算方法，这样可以方式浪费时间去计算显示以外的cell

5.不要使用ClearColor，无背景色，透明度也不要设置为0

    渲染耗时比较长

6.使用局部更新

    如果只是更新某组的话，使用reloadSection进行局部更新

7.加载网络数据，下载图片，使用异步加载，并缓存
8.少使用addView 给cell动态添加view
9.按需加载cell，cell滚动很快时，只加载范围内的cell
10.不要实现无用的代理方法，tableView只遵守两个协议
11.缓存行高：estimatedHeightForRow不能和HeightForRow里面的layoutIfNeed同时存在，这两者同时存在才会出现“窜动”的bug。所以我的建议是：只要是固定行高就写预估行高来减少行高调用次数提升性能。如果是动态行高就不要写预估方法了，用一个行高的缓存字典来减少代码的调用次数即可

####4.block
面试题：

block是存储在堆中还是栈中？

- 1）默认情况下，block存储在栈中，如果对block进行一个copy操作，block会转移到堆中。
- 2）如果block在栈中，block中访问了外界的对象，那么不会对对象进行retain操作；
- 3）但如果block在堆中，block中访问了外界的对象，那么会对外界的对象进行一次retain操作。
- 4）如果在block中访问了外界的对象，一定要给对象加上 __block，只要加上了 __block，哪怕block在堆中，也不会对外界的对象进行retain操作。


####5.block 试题
```objc
@implementation ViewController

NSInteger globalCounter = 0;

- (void)viewDidLoad {
    [super viewDidLoad];

    [self test];
}


- (void) test{

    NSInteger localCounter = 42;
    __block char localCharacter;

    void (^aBlock)(void) = ^(void){
        globalCounter = localCounter;
        localCharacter = 'a';
        NSLog(@"1 --- %zd --- %c", globalCounter, localCharacter);
    };

    ++localCounter;
    localCharacter = 'b';
    NSLog(@" 2--- %zd --- %c", localCounter, localCharacter);

    aBlock();

    NSLog(@" 3--- %zd --- %c", localCounter, localCharacter);

    /*

     ViewController.m:41	 2--- 43 --- b
     ViewController.m:36	1 --- 42 --- a
     ViewController.m:45	 3--- 43 --- a

     */
}

```
