# Size和center

- 注意:
    - size从frame取出,如果size从frame取出来,一定要先设置size,再设置center(frame 是从左上角开始扩展).
            如果一个刚新建的 View, 比如UIView *redView = [[UIView alloc] init]; 是没有大小的, 如果先设置 center, 再设置frame, 那么 center 的值就无效了, redView 是从左上角开始布局
    - size从bounds取出,就不需要关心顺序(bounds 是从中心点开始拓展)

```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    UIView *redView = [[UIView alloc] init];

    redView.backgroundColor = [UIColor redColor];

    [self.view addSubview:redView];

    // center
    redView.center = self.view.center;

    // size
    CGRect bounds = redView.bounds;
    bounds.size = CGSizeMake(200, 200);
    redView.bounds = bounds;

    // 1.size从frame取出,如果size从frame取出来,一定要先设置size,在设置center(frame 是从左上角开始扩展)

    // 2.size从bounds取出,就不需要关心顺序(bounds 是从中心点开始拓展)
}
```
