# assign和weak的区别


- 面试题: weak 和 assign 区别
    - 使用 weak 和 assign 时, 计数器都不会+1, 但是, 对象销毁的时候, 使用 weak 修饰的指针会被清空, 使用 assign 修饰的指针不会被清空

 - weak:
    - weak:常用于控件,代理属性
    - weak: 弱指针,被__weak修饰.作用:计数器不会+1, 当一个对象被销毁的时候,这个指针会清空

 - assign :
    - 1.控件为什么不能使用assign?
    ```
        如果使用了就会报坏内存访问
    ```
    - 2.为什么会报坏内存访问
    ```
    (2013 的时候才开始有 ARC, 那个时候没有 weak, 采用的是__unsafe_unretained)
    __unsafe_unretained:只有非ARC中才使用,
    __unsafe_unretained:使用assign,被"__unsafe_unretained",计算器不会+1,当一个对象被销毁的时候,不会清空指针
    ```


```objc
#import "ViewController.h"

@interface ViewController ()

@property (nonatomic, weak) UIView *redView;

@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];


    UIView *redView = [[UIView alloc] init];
    redView.backgroundColor = [UIColor redColor];
    //    [self.view addSubview:redView]; // 使得 viewDidLoda 方法执行完毕之后, redView 销毁...如果使用 weak 修饰, _redView 指针会被清空; 如果使用 assign 修饰, _redView 指针不会被清空, 会报坏内存访问的错误!!!!!
    _redView = redView;


}

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    NSLog(@"%@",_redView);
    _redView.frame = CGRectMake(50, 50, 200, 200);

}

@end
```
