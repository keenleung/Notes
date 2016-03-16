# hitTest:withEvent方法

####hitTest:withEvent
__1)什么时候调用?__

当一个事件传递给当前View时调用

__2)作用?__

寻找最适合的View

__3)返回值__

找到的最适合的View,如果没有找到最适合的View,返回是一个nil.

__4)实现细节__

```objc
-(UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {

    // hitText 方法的执行过程
    //1.判断当前View能否接收事件
    if(self.userInteractionEnabled == NO || self.hidden == YES || self.alpha <= 0.01) {
        return nil;
    }

    //2.判断当前点在不在View身上
    if (![self pointInside:point withEvent:event]) {
        return nil;
    }

    //3.从后往前遍历自己的子控件.让自己的子控件寻找最适合的View;
    int count = (int)self.subviews.count;
    for (int i = count - 1; i >= 0; i--) {
        //取出子控件
        UIView *childV = self.subviews[i];
        //把当前点的坐标系转换成子控件身上的坐标系
        CGPoint childP = [self convertPoint:point toView:childV];
        UIView *fitView = [childV hitTest:childP withEvent:event];
        if (fitView) {
            return fitView;
        }
    }

    //4.没有找到比自己更适合的view,那么它自己就是最适合的view
    return self;
}

```

####pointInside: withEvent:
__1)什么时候调用?__

在hitTest方法当中调用

__2)作用?__

判断当前点在不在方法调用者身上.

__3)返回值__

如果返回yes,在View身,如果 为NO,不在View身上.

__4)注意点?__

传入的点point,必须得与方法调用者在同一个坐标系.

```objc
- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event {
    return NO;
}
```


####hitTest 案例

#####1)案例一

点击绿色 view, 如果点在按钮上,按钮来处理点击事件

![显示图片](images/Snip20160307_6.png)

实现细节

GreenView.m

```objc
#import "GreenView.h"

@interface GreenView()
@property (nonatomic, weak) IBOutlet UIButton *redBtn;
@end

@implementation GreenView

- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {

    //判断当前点在不在按钮身上.
    //把当前点转换成按钮坐标系上面的点
    CGPoint redBtnP = [self convertPoint:point toView:self.redBtn];
    if ([self.redBtn pointInside:redBtnP withEvent:event]) {
        return self.redBtn;
    }else {
        return [super hitTest:point withEvent:event];
    }
      //return [super hitTest:point withEvent:event];
}

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {

    NSLog(@"%s",__func__);
}

@end
```


#####2)案例二

点击弹出对话框

![显示图片](images/Snip20160307_11.png)

实现细节:

ViewController.m

点击按钮,弹出对话框

```objc
- (IBAction)btnClick:(ChatBtn *)sender {

    //添加一个子控件
    UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
    [btn setImage:[UIImage imageNamed:@"对话框"] forState:UIControlStateNormal];
    [btn setImage:[UIImage imageNamed:@"小孩"] forState:UIControlStateHighlighted];
    btn.frame = CGRectMake(50, -80, 100, 80);
//    btn.frame = CGRectMake(0, 0, 100, 100);

    btn.backgroundColor = [UIColor grayColor];

    sender.popBtn = btn;

    [sender addSubview:btn];
}
```

按钮的类型: ChatBtn

ChatBtn.h
```objc
@interface ChatBtn : UIButton

/** <#注释#>*/
@property (nonatomic, weak) UIButton *popBtn;

@end
```

ChatBtn.m
```objc

@implementation ChatBtn

//如果 一个子控件超出父控件的大小, 默认情况下是不能处理的事件.
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {

    //如果点在自己的子控件上面.让子控件处理事件.
     NSLog(@"%@",self.popBtn);
    if (self.popBtn) {
        CGPoint popBtnP = [self convertPoint:point toView:self.popBtn];
        if ( [self.popBtn pointInside:popBtnP withEvent:event]) {
            return self.popBtn;
        }else {
            return [super hitTest:point withEvent:event];
        }
    }
    return   [super hitTest:point withEvent:event];
}

- (void)touchesMoved:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {

    //获取UITouch
    UITouch *touch = [touches anyObject];
    //获取当前手指的点
    CGPoint curP = [touch locationInView:self];
    //获取上一个手指所在的点
    CGPoint preP = [touch previousLocationInView:self];
    //计算偏移量
    CGFloat offsetX = curP.x - preP.x;
    CGFloat offsetY =curP.y - preP.y;
    //平移
    self.transform = CGAffineTransformTranslate(self.transform, offsetX, offsetY);
}

@end
```
