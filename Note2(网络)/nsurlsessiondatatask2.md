# NSURLSessionDataTask实现离线断点续传(优化)

## 知识点
- 记录文件写入情况
    - 方式一: 文件句柄
    - 方式二: 输出流
        - 创建, 打开

            ```objc
            // 4.创建输出流
            /*
            第一个参数:路径 文件数据保存的位置
            第二个参数:是否是追加
            如果输出流发现指向路径下面文件不存在那么会自动的创建一个空的文件
            */
            NSOutputStream *stream = [[NSOutputStream alloc] initToFileAtPath:fullPath append:YES];

            // 5.打开输出流
            [stream open];
            ```
        - 写入数据
        ```objc
        // 1.使用输出流来写入数据
    [self.stream write:data.bytes maxLength:data.length];
        ```
        - 关闭输出流
        ```objc
        // 关闭输出流
        [self.stream close];
        ```
- session 对象的创建(代理方式)注意点:
    - session 创建过程中, 如果指定代理执行的队列为nil, 那么代理方法是在子线程中调用的
    - session 设置了代理, 就会对代理指向的类进行强引用, 需要手动释放 invalidateAndCancel
    ```objc
        // 创建
        _session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];

        // 释放
        - (void)viewWillDisappear:(BOOL)animated{
        [self viewWillDisappear:animated];

        // 手动释放 session
        if (self.session) {
            [self.session invalidateAndCancel];
        }
    }

    ```


```objc
// 下载文件的全路径
#define fullPath [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:@"minion_01.mp4"]

// 记录下载文件的总大小
#define totalSizefullPath [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:@"xmg.xmg"]


#import "ViewController.h"

@interface ViewController ()<NSURLSessionDataDelegate>

/** progressView*/
@property (weak, nonatomic) IBOutlet UIProgressView *progressView;

/** 总文件的大小*/
@property (nonatomic, assign) NSInteger totalSize;

/** 文件句柄*/
//@property (nonatomic, strong) NSFileHandle *handle;
@property (nonatomic, strong) NSOutputStream *stream;

/** 当前获取的数据大小*/
@property (nonatomic, assign) NSInteger currentSize;

/** 文件下载任务*/
@property (nonatomic, strong) NSURLSessionDataTask *dataTask;

/** 会话对象*/
@property (nonatomic, strong) NSURLSession *session;

@end

@implementation ViewController

- (void)viewWillDisappear:(BOOL)animated{
    [self viewWillDisappear:animated];

    // 手动释放 session
    if (self.session) {
        [self.session invalidateAndCancel];
    }
}


- (void)viewDidLoad{
    [super viewDidLoad];

    // 检查文件是否被下载过
    NSDictionary *dictInfo = [[NSFileManager defaultManager] attributesOfItemAtPath:fullPath error:nil];

    // 获取文件的大小
    NSInteger size = [dictInfo[@"NSFileSize"] integerValue];
    self.currentSize = size;

    // 获取文件的总大小
    NSData *data = [NSData dataWithContentsOfFile:totalSizefullPath];
    if (data) {
        NSInteger totalSize = [[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] integerValue];
        if (size > 0 && totalSize > 0) {

            // 计算进度
            CGFloat progress = 1.0 * self.currentSize / totalSize;

            self.progressView.progress = progress;
        }
    }

}

- (NSURLSession *)session{
    if (!_session) {
        /*

         补充知识点:
         1)session 创建过程中, 如果指定代理执行的队列为nil, 那么代理方法是在子线程中调用的
         2)session 设置了代理, 就会对代理指向的类进行强引用, 需要手动释放

         */
        _session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];
    }
    return _session;
}


- (NSURLSessionDataTask *)dataTask{

    if (!_dataTask) {

        NSString *urlStr = @"http://120.25.226.186:32812/resources/videos/minion_01.mp4";

        // 1.确定 URL
        NSURL *url = [NSURL URLWithString:urlStr];

        //检查文件是否下载过
        NSDictionary *dictInfo = [[NSFileManager defaultManager] attributesOfItemAtPath:fullPath error:nil];

        //获得文件的大小
        //NSInteger size = [dictInfo fileSize];;
        NSInteger size = [dictInfo[@"NSFileSize"] integerValue];

        self.currentSize = size;

        //        if([[NSFileManager defaultManager] fileExistsAtPath:fullPath]){
        //            self.currentSize = [NSData dataWithContentsOfFile:fullPath].length;
        //        }

        // 2.创建可变的请求对象
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        // 2.1设置请求范围
        /*
         bytes=500-1000 请求从第500到1000个字节的数据
         bytes=-500   请求从第0到500个字节的数据
         bytes= 500-     请求从第500个字节开始的数据,直到整个文件结尾
         */
        NSString *range = [NSString stringWithFormat:@"bytes=%zd-", self.currentSize];
        [request setValue:range forHTTPHeaderField:@"Range"];

        // 3.创建 session 对象

        // 4.创建 task
        _dataTask = [self.session dataTaskWithRequest:request];
    }
    return _dataTask;
}


#pragma mark --------------------------
#pragma mark NSURLSessionDataDelegate
// 接收到响应
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler{

    // expectedContentLength 获得的是本次网络请求的数据大小
    // 1.得到文件的总大小
    // 文件总大小 = 当前请求的文件大小 + 之前已经下载的文件的大小
    // 10 = 10 + 0
    // 10 = 7 + 3
    self.totalSize = response.expectedContentLength + self.currentSize;

    // 把文件的总大小保存起来
    NSData *data = [[NSString stringWithFormat:@"%zd", self.totalSize] dataUsingEncoding:NSUTF8StringEncoding];
    [data writeToFile:totalSizefullPath atomically:YES];

    if (self.currentSize == 0) {

        // 3.创建一个空的文件
        /*
         第一个参数:文件路径
         第二个参数:文件的数据内容
         第三个参数:文件的属性 nil
         */
        [[NSFileManager defaultManager] createFileAtPath:fullPath contents:nil attributes:nil];
    }


    // 4.创建输出流
    /*
     第一个参数:路径 文件数据保存的位置
     第二个参数:是否是追加
     如果输出流发现指向路径下面文件不存在那么会自动的创建一个空的文件
     */
    NSOutputStream *stream = [[NSOutputStream alloc] initToFileAtPath:fullPath append:YES];

    // 5.打开输出流
    [stream open];

    self.stream = stream;

    completionHandler(NSURLSessionResponseAllow);

    NSLog(@"接收到响应...");

    NSLog(@"fullPath = %@", fullPath);
}

// 接收到数据
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data{

    // 1.使用输出流来写入数据
    [self.stream write:data.bytes maxLength:data.length];

    // 2.累加当前已经下载的数据的大小
    self.currentSize += data.length;

    // 2.计算文件的下载进度
    CGFloat progress = 1.0 * self.currentSize / self.totalSize;

    self.progressView.progress = progress;

    NSLog(@"接收到数据...  %lf", progress);
}

// 请求结束
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error{

    // 关闭输出流
    [self.stream close];
    self.stream = nil;

    NSLog(@"%@", fullPath);

    NSLog(@"请求结束...");
}

#pragma mark --------------------------
#pragma mark 按钮点击事件
// 开始
- (IBAction)beginButtonDidClicked:(id)sender {

    [self.dataTask resume];

    NSLog(@"开始...");
}

// 暂停
- (IBAction)suspendButtonDidClicked:(id)sender {

    [self.dataTask suspend];

    NSLog(@"暂停...");
}

// 取消
- (IBAction)cancelButtonDidClicked:(id)sender {

    // 不可恢复
    [self.dataTask cancel];
    self.dataTask = nil;

    NSLog(@"取消...");
}

// 继续下载
- (IBAction)continueButtonDidClicked:(id)sender {

    [self.dataTask resume];

    NSLog(@"继续下载...");
}

@end
```
