# GestureRecognize
####手势
- UITapGestureRecognizer(敲击)

- UIPinchGestureRecognizer(捏合，用于缩放)

- UIPanGestureRecognizer(拖拽)

- UISwipeGestureRecognizer(轻扫)

- UIRotationGestureRecognizer(旋转)

- UILongPressGestureRecognizer(长按)

####支持多个手势?
需要遵循:UIGestureRecognizerDelegate 协议,后实现:
```objc
//是否允许支持多个手势
// 需要给手势设置代理
-(BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer {

    return YES;
}
```

####拖动手势
```objc
//拖动手势
- (void)panGes {
    //1.创建手势
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];
    //2.添加手势
    [self.imageV addGestureRecognizer:pan];

}

//当拖动时调用
- (void)pan:(UIPanGestureRecognizer *)pan {

    //获取偏移量(相对于最原始的值)
    CGPoint transP = [pan translationInView:pan.view];

    //NSLog(@"transP=%@",NSStringFromCGPoint(transP));

    self.imageV.transform = CGAffineTransformTranslate(self.imageV.transform, transP.x, transP.y);

    //复位(相对于上一次的操作)
    [pan setTranslation:CGPointZero inView:pan.view];

    // 不能用以下的方法,有问题
    //self.imageV.transform = CGAffineTransformMakeTranslation(transP.x, transP.y);
}
```


####捏合手势
```objc
//捏合手势
- (void)pincGes {

    //1.创建手势
    UIPinchGestureRecognizer *pinch = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(pinch:)];

    pinch.delegate = self;

    //2.添加手势
    [self.imageV addGestureRecognizer:pinch];
}

//当缩放时(捏合)会调用
- (void)pinch:(UIPinchGestureRecognizer *)pinch {

    self.imageV.transform = CGAffineTransformScale(self.imageV.transform, pinch.scale, pinch.scale);
    //复位
    [pinch setScale:1];

    // 或者:
    // self.imageV.transform = CGAffineTransformMakeScale(pinch.scale, pinch.scale);
}
```

####旋转手势
```objc
//旋转手势
- (void)rotationGes {

    //1.创建手势
    UIRotationGestureRecognizer *rotation = [[UIRotationGestureRecognizer alloc] initWithTarget:self action:@selector(rotation:)];

    rotation.delegate = self;

    //2.添加手势
    [self.imageV addGestureRecognizer:rotation];
}


//当旋转时调用
- (void)rotation:(UIRotationGestureRecognizer *)rotation {

    self.imageV.transform = CGAffineTransformRotate(self.imageV.transform, rotation.rotation);
    //复位
    [rotation setRotation:0];

    // 或者:
    // self.imageV.transform = CGAffineTransformMakeRotation(rotation.rotation);
}
```

####轻扫手势
```objc
// 轻扫手势
- (void) swipeGesture{
    //轻扫手势

    // 1.创建手势
    // 一个手势对应一个方向,多个方向的时候,会发生混乱
    // 右
    UISwipeGestureRecognizer *swipe = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];
    // 默认轨扫的方向向右轻扫
    swipe.direction = UISwipeGestureRecognizerDirectionRight;

    // 左
    UISwipeGestureRecognizer *swipe1 = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];
    swipe1.direction = UISwipeGestureRecognizerDirectionLeft;

    UISwipeGestureRecognizer *swipe2 = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];
    swipe2.direction = UISwipeGestureRecognizerDirectionUp;

    UISwipeGestureRecognizer *swipe3 = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];
    swipe3.direction = UISwipeGestureRecognizerDirectionDown;


    //2.添加手势
    [self.imageV addGestureRecognizer:swipe];
    [self.imageV addGestureRecognizer:swipe1];
    [self.imageV addGestureRecognizer:swipe2];
    [self.imageV addGestureRecognizer:swipe3];
}

//轻扫手势发生时调用
- (void)swipe:(UISwipeGestureRecognizer *)swipe {
    if (swipe.direction == UISwipeGestureRecognizerDirectionUp) {
        NSLog(@"Up");
    }else if (swipe.direction == UISwipeGestureRecognizerDirectionDown){
        NSLog(@"Down");
    }else if (swipe.direction == UISwipeGestureRecognizerDirectionLeft){
        NSLog(@"Left");
    }else if(swipe.direction == UISwipeGestureRecognizerDirectionRight){
        NSLog(@"Right");
    }
}
```

####长按手势
```objc
//长按手势
- (void)longPress {
    //1.创建手势
    UILongPressGestureRecognizer *longP = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longP:)];
    //2.添加手势
    [self.imageV addGestureRecognizer:longP];

}

//当长按时调用,长按方法,如果 移动时会持续调用
- (void)longP: (UILongPressGestureRecognizer *)longP {


    if (longP.state == UIGestureRecognizerStateBegan) {
         NSLog(@"开始长按");
    } else if (longP.state == UIGestureRecognizerStateChanged) {
        NSLog(@"长按时移动");
    } else if (longP.state == UIGestureRecognizerStateEnded) {
        NSLog(@"手指离开");
    }

}

```


####点按手势
```objc
//点按手势
- (void)tapGes {
    //1.创建手势
    UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tap)];
    tap.delegate = self;
    //2.添加手势
    [self.imageV addGestureRecognizer:tap];
}
//当点击ImageV时调用
- (void)tap {
    NSLog(@"点击了右边");
}

// 是否允许接收手指.
// 需要遵循 UIGestureRecognizerDelegate 协议
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch {

    //获取当前手指的点 (以 self.imageV 的左上角为坐标原点)
    CGPoint curP = [touch locationInView:self.imageV];

    if (curP.x > self.imageV.bounds.size.width * 0.5) {
        //在右侧
        return YES;
    }else {
        //在左侧
        return NO;
    }

}
```
