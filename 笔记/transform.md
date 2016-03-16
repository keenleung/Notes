# transform的使用

__(1)平移 : Translate__

```objc
[UIView animateWithDuration:0.5 animations:^{
        //TransformMake,相对于最原始的形变进行操作
        //self.redView.transform = CGAffineTransformMakeTranslation(100, 0);
        //在哪一个形变基础上进行操作
        self.redView.transform = CGAffineTransformTranslate(self.redView.transform, 50, 50);
    }];
```

<br />
__(2)旋转 : Rotation__
```objc
 [UIView animateWithDuration:0.5 animations:^{
        //self.redView.transform = CGAffineTransformMakeRotation(M_PI_4);
        self.redView.transform = CGAffineTransformRotate(self.redView.transform, M_PI_4);
    }];
```

<br />
__(3)缩放 : Scale__
```objc
[UIView animateWithDuration:0.5 animations:^{
        //self.redView.transform = CGAffineTransformMakeScale(1.5, 1.5);
        self.redView.transform = CGAffineTransformScale(self.redView.transform, 0.8, 0.8);
    }];
```
