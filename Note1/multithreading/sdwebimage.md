#15 SDWebImage框架
##1. SDWebImage基本使用
```objc
1）下载图片并显示（内存缓存&磁盘缓存）
    /*
     第一个参数：图片的url地址
     第二个参数：设置的占位图片
     */
    [self.imageView sd_setImageWithURL:[NSURL URLWithString:@"http://www.chinanews.com/cr/2014/0108/1576296051.jpg"] placeholderImage:[UIImage imageNamed:@"Snip20160112_4"]];

2）下载图片显示并计算下载进度（内存缓存&磁盘缓存&下载进度）
    -(void)download2
    {
        /*
         第一个参数：图片的url地址
         第二个参数：设置的占位图片
         第三个参数：下载图片选项（策略）
         第四个参数：进度回调block
            receivedSize:已经下载的数据大小
            expectedSize：图片的总大小
         第五个参数：completed图片下载结束回调（成功|失败）
            image：下载后得到的图片，如果下载失败，那么image的值为nil
            error：错误信息，如果失败，则error有值
            cacheType：图片来源（枚举：内存缓存|磁盘缓存|直接下载）
            imageURL：下载图片的url
         */
        [self.imageView sd_setImageWithURL:[NSURL URLWithString:@"http://attachments.gfan.com/forum/attachments2/day_110715/110715164125355ea61c6dcfa5.jpg"]  placeholderImage:[UIImage imageNamed:@"Snip20160112_4"] options:SDWebImageProgressiveDownload progress:^(NSInteger receivedSize, NSInteger expectedSize) {
            NSLog(@"%f",1.0 * receivedSize/expectedSize);

        } completed:^(UIImage *image, NSError *error, SDImageCacheType cacheType, NSURL *imageURL) {

            NSLog(@"%@",[NSThread currentThread]);
            switch (cacheType) {
                case SDImageCacheTypeNone:
                    NSLog(@"直接下载");
                    break;
                case SDImageCacheTypeDisk:
                    NSLog(@"磁盘缓存");
                    break;
                case SDImageCacheTypeMemory:
                    NSLog(@"内存缓存");
                    break;
                default:
                    break;
            }
        }];
    }
3）下载图片不显示并监听下载进度（内存缓存&磁盘换次&下载进度）
    -(void)download3
    {
        //使用管理者下载图片
        [[SDWebImageManager sharedManager] downloadImageWithURL:[NSURL URLWithString:@"http://www.chinanews.com/cr/2014/0108/1576296051.jpg"] options:0 progress:^(NSInteger receivedSize, NSInteger expectedSize) {

             NSLog(@"%f",1.0 * receivedSize/expectedSize);

        } completed:^(UIImage *image, NSError *error, SDImageCacheType cacheType, BOOL finished, NSURL *imageURL) {

            NSLog(@"+++++%@",[NSThread currentThread]);
            self.imageView.image = image;

            switch (cacheType) {
                case SDImageCacheTypeNone:
                    NSLog(@"直接下载");
                    break;
                case SDImageCacheTypeDisk:
                    NSLog(@"磁盘缓存");
                    break;
                case SDImageCacheTypeMemory:
                    NSLog(@"内存缓存");
                    break;
                default:
                    break;
            }
        }];
    }
4）下载图片不显示且不做任何的缓存处理
    -(void)download4
    {
        [[SDWebImageDownloader sharedDownloader] downloadImageWithURL:[NSURL URLWithString:@"http://img3.3lian.com/2006/013/08/20051103121420947.gif"] options:0 progress:^(NSInteger receivedSize, NSInteger expectedSize) {

             NSLog(@"%f",1.0 * receivedSize/expectedSize);
            NSLog(@"%@",[NSThread currentThread]);
        } completed:^(UIImage *image, NSData *data, NSError *error, BOOL finished) {

            NSLog(@"%@",[NSThread currentThread]);

            [[NSOperationQueue mainQueue] addOperationWithBlock:^{
                self.imageView.image = [UIImage sd_animatedGIFWithData:data];
            }];

        }];
    }

5）接收到系统级内存警告时如何处理（面试）
    //（1）取消当前正在进行的所有下载操作
    [[SDWebImageManager sharedManager] cancelAll];

    //（2）清除缓存数据
    //cleanDisk：删除过期的文件数据，计算当前未过期的已经下载的文件数据的大小，如果发现该数据大小大于我们设置的最大缓存数据大小，那么程序内部会按照按文件数据缓存的时间从远到近删除，知道小于最大缓存数据为止。
    //clearMemory:直接删除文件，重新创建新的文件夹
    //[[SDWebImageManager sharedManager].imageCache cleanDisk];
    [[SDWebImageManager sharedManager].imageCache clearMemory];

 6）播放gif图片
    （1）播放GiF图片部分过程解析
        a.把用户传入的gif图片->NSData
        b.根据该Data创建一个图片数据源（NSData->CFImageSourceRef）
        c.计算该数据源中一共有多少帧，把每一帧数据取出来放到图片数组中
        d.根据得到的数组+计算的动画时间-》可动画的image
        e.[UIImage animatedImageWithImages:images duration:duration];
    （2）如何使用
        -(void)gif
        {
            //self.imageView.image = [UIImage imageNamed:@"123"];  不可用
            UIImage *image = [UIImage sd_animatedGIFNamed:@"123"];
            self.imageView.image = image;
        }
```

#2. SDWebImage内部实现细节
- clear和clean的区别
    - clear:直接把之前的缓存文件删除,然后重新创建一个空的
    - clean:删除"过期"的缓存,然后计算剩余的缓存大小,和设置的最大缓存大小比较.如果发现超出那么就继续删除,按照文件的创建时间来删除(由远到进)
- 判断当前图片类型：只判断图片二进制数据的第一个字节
- 默认的缓存周期：1周
- 播放gif |#import <ImageIO/ImageIO.h>
- 缓存策略：默认情况下既做内存缓存又做磁盘缓存，下载图片前先检查内存缓存，再检查磁盘缓存
- 缓存的实现方式：采用了苹果推出的专门用来处理缓存的类NSCache
- 框架内部允许的最大并发数：6
- 对系统内存警告的处理方式：框架内部监听系统内存警告的通知，当发生后移除内存缓存中的所有对象
- 下载队列中对多个图片任务的处理方式：提供了FIFO和LIFO两种方式，默认为FIFO
- 如何下载图片：采用NSURLConnection发送网络请求，在其代理方法中接收数据并处理进度回调等工作
- 请求超时的设定：15秒
- 磁盘缓存图片的命名：以该图片的URL进行MD5散列加密【echo -n "url" |MD5】
- 缓存路径：~/Library/Caches/default/com.hackemist.SDWebImageCache.default
- key---URL(如何优化),黑名单(当一个URL请求失败后,会被添加到黑名单中,可以有效的防止一个错误的URL被多次尝试下载)

===========================================================================================
