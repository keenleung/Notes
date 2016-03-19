##UIScrollView的基本使用

- contentSize：设置内容尺寸(滚动的范围)
        可滚动的尺寸:contentSize的尺寸 减去 scrollView的尺寸
        注意点:contentSize的尺寸小于或者等于scrollView的尺寸也是不可以滚动的
```objc
self.scrollView.contentSize = CGSizeMake(301, 201);
```

- contentOffset：内容的偏移量
```objc
// 3.内容的偏移量
    // 控制内容滚动的位置
    // 得知内容滚动的位置
    self.scrollView.contentOffset = CGPointMake(0, -100);
```
- contentInset：内边距
```objc
self.scrollView.contentInset = UIEdgeInsetsMake(100, 0, 0, 0);
```


- clipsToBounds：默认设置了剪裁属性为YES
        self.scrollView.clipsToBounds = YES;
- scrollEnabled： 是否能够滚动
```objc
 self.scrollView.scrollEnabled = NO;
```
- userInteractionEnabled：是否能跟用户交互
```objc
self.scrollView.userInteractionEnabled = NO;
```
注意点:如果设置该属性为NO，scrollView以及它所有的子控件都不能跟用户交互
<br />

- bounces： 是否有弹簧效果
```objc
    // 3.是否有弹簧效果
    self.scrollView.bounces = NO;

    // 4.不管有没有设置contentSize,总是有弹簧效果
    self.scrollView.alwaysBounceHorizontal = YES;
    self.scrollView.alwaysBounceVertical = YES;
```

- showsHorizontalScrollIndicator, showsVerticalScrollIndicator: 是否显示滚动条
 ```objc
     // 5.是否显示滚动条
    self.scrollView.showsHorizontalScrollIndicator = NO;
    self.scrollView.showsVerticalScrollIndicator = NO;
```

- pagingEnabled：开启分页
    - 标准：以scrollView的尺寸为一页
    - 例子：
```objc
// 3.开启分页功能
    // 标准:以scrollView的尺寸为一页
    self.scrollView.pagingEnabled = YES;
```


####代理
需要遵守UIScrollViewDelegate协议及设计代理：
```objc
// 4.设置代理
    scrollView.delegate = self;
```
常用的代理方法：
- -(void)`scrollViewDidScroll`:(UIScrollView *)scrollView;
        当scrollView正在滚动的时候就会自动这个方法

- -(void)`scrollViewWillBeginDragging`:(UIScrollView *)scrollView
        用户即将开始拖拽scrollView时,就会调用这个方法

- -(void)`scrollViewWillEndDragging`:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity
        用户即将停止拖拽scrollView时,就会调用这个方法

- -(void)`scrollViewDidEndDragging`:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate
        用户已经停止拖拽scrollView时,就会调用这个方法

- -(void)`scrollViewDidEndDecelerating`:(UIScrollView *)scrollView
        减速完毕的时候回调用这个方法(停止滚动)

- -(UIView *)`viewForZoomingInScrollView`:(UIScrollView *)scrollView
        利用手势，实现内容缩放

```objc
 //  返回一个需要缩放的子控件(scrollView)
- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView
{
    return self.imageView;
}
```
__需要设置缩放比例__：
```objc
// 3.设置缩放比例
    self.scrollView.maximumZoomScale = 2.0;
    self.scrollView.minimumZoomScale = 0.5;
```

##定时器
创建定时器：
```objc
// 创建一个自动执行任务的定时器
    self.timer = [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(nextPage:) userInfo:@"123" repeats:YES];

    // NSDefaultRunLoopMode(默认):同一时间只能执行一个任务
    // NSRunLoopCommonModes(通用):可以分配一定的时间处理其他任务

    // 作用:修改timer在runLoop中的模式为NSRunLoopCommonModes;
    // 目的:不管主线程在做什么操作,都会分配一定的时间处理timer
    [[NSRunLoop mainRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes];
```

停止定时器：
```objc
    [self.timer invalidate];
```


