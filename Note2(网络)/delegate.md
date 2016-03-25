# Delegate
- 特点:能够直接把文件下载到沙盒中,我们需要做文件剪切处理(不会有内存飙升的问题)
- 应用:通用
- 缺点:无法实现离线断点下载


##基本使用

- 遵循 NSURLSessionDownloadDelegate 协议

```objc
#import "ViewController.h"

@interface ViewController ()<NSURLSessionDownloadDelegate>

@property (nonatomic, strong) NSURLSession *session;
@property (nonatomic, strong) NSURLSessionDownloadTask *downloadTask;
@property (weak, nonatomic) IBOutlet UIProgressView *progressView;

/** 恢复下载的数据*/
@property (nonatomic ,strong) NSData *resumeData;
@end

...
- (NSURLSession *)session{
    if (!_session) {
        _session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[[NSOperationQueue alloc] init]];
    }
    return _session;
}
...



// 特点:能够直接把文件下载到沙盒中,我们需要做文件剪切处理(不会有内存飙升的问题)
// 应用:通用
// 缺点:无法实现离线断点下载
- (void) downloadFileDataWithDelegate{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://imgsrc.baidu.com/forum/pic/item/6af8dd3533fa828b2a734b0dfd1f4134950a5ad7.jpg"];
    //NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_01.mp4"];


    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 3.创建 session 对象
    // 懒加载

    // 4.根据 session 创建 task
    NSURLSessionDownloadTask *downloadTask = [self.session downloadTaskWithRequest:request];

    // 5.执行 task
    [downloadTask resume];

    // 记录
    self.downloadTask = downloadTask;
}
```

## 常用代理方法
```objc
/**
 *  1.写数据的时候调用
 *  @param bytesWritten              本次下载数据的大小
 *  @param totalBytesWritten         当前已经下载数据的总大小
 *  @param totalBytesExpectedToWrite 文件的总大小
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite{

    CGFloat progress = 1.0 * totalBytesWritten / totalBytesExpectedToWrite;

    dispatch_async(dispatch_get_main_queue(), ^{

        self.progressView.progress = progress;
    });

    NSLog(@"当前的下载进度: %lf", progress);
}

/**
 *  恢复下载的时候调用
 *
 *  @param fileOffset         当前从什么位置开始下载
 *  @param expectedTotalBytes 文件的总大小
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didResumeAtOffset:(int64_t)fileOffset expectedTotalBytes:(int64_t)expectedTotalBytes{

    NSLog(@"didResumeAtOffset --- 恢复下载");
}

/**
 * 当下载完成调用
 *
 *  @param location     文件的临时存储路径(磁盘/tmp)
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location{

    NSLog(@"下载完成 : %@", location);

    // 1.获取文件的名称
    NSString *fileName = downloadTask.response.suggestedFilename;

    // 2.拼接文件的全路径
    NSString *fullPath = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:fileName];

    // 3.执行剪切操作
    [[NSFileManager defaultManager] moveItemAtURL:location toURL:[NSURL fileURLWithPath:fullPath] error:nil];

    NSLog(@"转移完成: %@", fullPath);
}

/**
 *  当整个请求结束的时候调用
 *
 *  @param error   错误信息
 */
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error{

    NSLog(@"didCompleteWithError --- 请求结束!");
}
```

## 开始, 暂停, 取消, 继续

- 暂停
        [self.downloadTask suspend]
- 继续
        [self.downloadTask resume];
- 取消
        //cancel :是不可以恢复
        //cancelByProducingResumeData:可以恢复的
        [self.downloadTask cancelByProducingResumeData:^(NSData * _Nullable resumeData) {

            self.resumeData = resumeData;
        }];

```objc
// 开始
- (IBAction)startButtonDidClicked:(id)sender {

    [self downloadFileDataWithDelegate];
}

// 暂停
- (IBAction)suspendButtonDidClicked:(id)sender {

    NSLog(@"暂停....");

    // 暂停任务
    [self.downloadTask suspend];
}

// 取消
- (IBAction)cancelButtonDidClicked:(id)sender {

    NSLog(@"取消....");

    //取消任务
    //cancel :是不可以恢复
    //cancelByProducingResumeData:可以恢复的
    [self.downloadTask cancelByProducingResumeData:^(NSData * _Nullable resumeData) {

        self.resumeData = resumeData;
    }];
}

// 继续
- (IBAction)continueButtonDidClicked:(id)sender {

    // 先判断用户当前是处于 暂停状态 还是处于 取消状态
    if (self.resumeData) {
        self.downloadTask = [self.session downloadTaskWithResumeData:self.resumeData];
    }

    // 继续下载
    [self.downloadTask resume];

    NSLog(@"继续下载....");
}
```
