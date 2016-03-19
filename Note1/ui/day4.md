# day4
##01-控制器的生命周期

当控制器的View加载完结时调用
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
}
```

当控制器的View即将显示的时候
```objc
- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
}
```

当控制器的View显示完结时调用
```objc
-(void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
}
```

当控制器的View将要布局子控件时调用
```objc
- (void)viewWillLayoutSubviews {
    [super viewWillLayoutSubviews];
}
```

当控制器的View布局子控件完毕时调用
```objc
- (void)viewDidLayoutSubviews {
    [super viewDidLayoutSubviews];
}
```

当控制器的View即将消失时调用
```objc
- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
}
```

当控制器的View消失完结时调用
```objc
- (void)viewDidDisappear:(BOOL)animated {
    [super viewDidDisappear:animated];
}
```

##02-导航控制器的一些注意点
####设置当前控制器不要调整ScrollView的contentInsets
iOS7之后,只要是导航控制器下的所有UIScrollView顶部都会添加额外的滚动区域.
```objc
//设置当前控制器不要调整ScrollView的contentInsets
self.automaticallyAdjustsScrollViewInsets = NO;
```

####设置导航条隐藏
```objc
self.navigationController.navigationBar.hidden = YES;
```

####设置导航条透明度
(1)

```objc
self.navigationController.navigationBar.alpha = 0;// 没有效果
```
设置导航条透明度为0,没有效果,还是原来的样子.

原因是因为导航条上面那一块并不直接是导航条,它是导航条里面的一个子控件.


(2)

考虑给它设置一个半透明的图片:
```objc
[self.navigationController.navigationBar setBackgroundImage:nil forBarMetrics:UIBarMetricsDefault];
```

在这里,有一个模式,必须要传默认UIBarMetricsDefault模式.

在这里发现设为nil的时候,也没有效果,那是因为系统它做了一层判断,它会判断如果传入的系统图片为空的话,它就会帮你生成一个半透明的图片,设置导航条的背景图片.


(3)

__解决办法__:

传入一张空的图片

```objc
[self.navigationController.navigationBar setBackgroundImage:[[UIImage alloc] init] forBarMetrics:UIBarMetricsDefault];
```

但是设置完后,发现有一根线,这根线其实是导航条的一个阴影.直接把它清空就行了.
```objc
[self.navigationController.navigationBar setShadowImage:[[UIImage alloc] init]];
```


####设置导航条文字的透明度
设置 titleView 为 UILabel,然后设置 label 的 textColor 属性即可

```objc
    //导航条上的View不允许直接设为透明.
    UILabel *titleL = [[UILabel alloc] init];
    titleL.textColor = [UIColor colorWithWhite:0 alpha:0];
    titleL.text = @"个人详情页";
    [titleL sizeToFit];
    self.titleL = titleL;
    //可以把文字的颜色搞成透明.
    self.navigationItem.titleView = titleL;
```

##03-沙盒操作

 plist存储, 偏好设置,归档

####plist 存储 :NSSearchPathForDirectoriesInDomains
说明:

数据存储是保存在手机里面的

plist文件存储一般都是存取字典和数组,直接写成plist文件,把它存到应用沙盒当中.

只有在ios当中才有plist存储,它是ios特有的存储方式.

__(1)获取沙盒根根路径__

每一个应用在手机当中都有一个文件夹,这个方法就是获取当前应用在手机里安装的文件.
```objc
NSString *homeDir = NSHomeDirectory();
NSLog(@"homeDir = %@",homeDir);
```

__(2)存储操作__

NSSearchPathForDirectoriesInDomains方法:在某个范围内搜索文件夹的路径.

<ol>
<li>directory:获取哪个文件夹</li>
<li>domainMask:在哪个路径下搜索</li>
<li>expandTilde:是否展开路径.</li>
</ol>

```objc
    //这个方法获取出的结果是一个数组.因为有可以搜索到多个路径.
    NSArray *array =  NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);

    //在这里,我们指定搜索的是Cache目录,所以结果只有一个,取出Cache目录
    NSString *cachePath = array[0];
    NSLog(@"%@",cachePath);

    //拼接文件路径
    NSString *filePathName = [cachePath stringByAppendingPathComponent:@"agePlist.plist"];

    //想要把这个字典存储为plist文件.
    //直接把字典写入到沙盒当中
    //用字典写, plist文件当中保存的是字典.
    NSDictionary *dict = @{@"age" : @18,@"name" : @"gaowei"};
    //获取沙盒路径
    //ToFile:要写入的沙盒路径
    [dict writeToFile:filePathName atomically:YES];

    //用数组写,plist文件当中保存的类型是数组.
    NSArray *dataArray = @[@56,@"asdfa"];
    //获取沙盒路径
    //ToFile:要写入的沙盒路径
    [dataArray writeToFile:filePathName atomically:YES];
```

__(3)读取操作__
```objc
    //这个方法获取出的结果是一个数组.因为有可以搜索到多个路径.
    NSArray *array =  NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);

    //在这里,我们指定搜索的是Cache目录,所以结果只有一个,取出Cache目录
    NSString *cachePath = array[0];
    NSLog(@"%@",cachePath);

    //拼接文件路径
    NSString *filePathName = [cachePath stringByAppendingPathComponent:@"agePlist.plist"];

    //从文件当中读取字典, 保存的plist文件就是一个字典,这里直接填写plist文件所存的路径
    NSDictionary *dict = [NSDictionary dictionaryWithContentsOfFile:filePathName];
    NSLog(@"%@",dict);

    //如果保存的是一个数组.那就通过数组从文件当中加载.
    NSArray *dataArray = [NSArray arrayWithContentsOfFile:filePathName];
    NSLog(@"%@",dataArray);
```

####偏好设置 :NSUserDefaults
__(1)写入操作__

```objc
    //偏好设置NSUserDefaults
    //底层就是封闭了一个字典,利用字典的方式生成plist文件
    //好处:不需要关心文件名(它会自动生成)快速进行键值对存储.
    NSUserDefaults *defautls = [NSUserDefaults standardUserDefaults];
    [defautls setObject:@"gaowei" forKey:@"name"];
    [defautls setBool:YES forKey:@"isBool"];
    [defautls setInteger:5 forKey:@"num"];

    //同步,立即写入文件.
    [defautls synchronize];
```

__(2)读取操作__
```objc
    //存是用什么key存的, 读的时候就要用什么key值取
    //存的时候使用的什么类型,取的时候也要用什么类型.
    NSString *str = [[NSUserDefaults standardUserDefaults] objectForKey:@"name"];
    BOOL isBool  = [[NSUserDefaults standardUserDefaults] boolForKey:@"isBool"];
    NSInteger num = [[NSUserDefaults standardUserDefaults] integerForKey:@"num"];
    NSLog(@"name =%@-isBool=%d-num=%ld",str,isBool,num);
```

####归档 :NSKeyedArchiver

__使用场景:__

归档一般都是保存自定义对象的时候,使用归档.因为plist文件不能够保存自定义对象.

如果一个字典当中保存有自定义对象,如果把这个字典写入到文件当中,它是不会生成plist文件的.


__(1)归档__ NSKeyedArchiver
```objc
    //获取沙盒临时目录
    NSString *tempPath = NSTemporaryDirectory();
    NSString *filePath = [tempPath stringByAppendingPathComponent:@"persion.data"];
    NSLog(@"%@",filePath);
    //archiveRootObject这个方法底层会去调用保存对象的encodeWithCoder方法,去询问要保存这个对象的哪些属性.
    //所以要实现encodeWithCoder方法, 告诉要保存这个对象的哪些属性.
    [NSKeyedArchiver archiveRootObject:persion toFile:filePath];
```

__(2)解档__ NSKeyedUnarchiver
```objc
    //获取沙盒临时目录
    NSString *tempPath = NSTemporaryDirectory();
    NSString *filePath = [tempPath stringByAppendingPathComponent:@"persion.data"];
    //NSKeyedUnarchiver会调用initWithCoder这个方法,来让你告诉它去获取这个对象的哪些属性.
    //所以我们在保存的对象当中实现initWithCoder方法.
    Persion *persion = [NSKeyedUnarchiver unarchiveObjectWithFile:filePath];

    NSLog(@"name=%@---age=%d",persion.name,persion.age);
```

__(3)注意:__

对象只有遵循了 __NSCoding__ 协议之后,才能够进行归档,解档操作,而且要重写 __encodeWithCoder__, __initWithCoder__方法


__遵守 NSCoding 协议__

Person.m

```objc
@interface Persion : NSObject<NSCoding>

@property (nonatomic, strong) NSString *name;
@property(nonatomic, assign) int age;

@end
```

Person.h

```objc
//只有遵守了NSCoding协议之后才能够实现这个方法.
//归档哪些属性.
-(void)encodeWithCoder:(NSCoder *)encode{

    [encode encodeObject:self.name forKey:@"name"];
    [encode encodeInt32:self.age forKey:@"age"];
}

//解档哪些属性
//initWithCoder什么时候调用:解析一个文件的时候就会调用.
-(instancetype)initWithCoder:(NSCoder *)decoder{

    //这个地方为什么没有[super initWithCoder]
    //是因为它的父类没有遵守NSCoding协议
    if (self = [super init]) {

       //要给它里面的属性进行赋值,外界取得对象时访问该属性,里面才会用值.
       self.age = [decoder decodeInt32ForKey:@"age"];
       self.name = [decoder decodeObjectForKey:@"name"];

    }
    return self;
}
```
