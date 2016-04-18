####1.SDWebImage清除缓存

```objc
#import <UIImageView+WebCache.h>

..
// 手动清除缓存
[[SDImageCache sharedImageCache] clearMemory];

..
```

####2.在 view 上执行 modal 操作
执行modal操作时，只能是在控制器上执行，如果是在 view 上，比如 xib de view，则可以直接获取窗口的根控制器，然后再执行modal
*![显示图片](../images/14-1.jpg)*
