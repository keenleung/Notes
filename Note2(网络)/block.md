# Block方式
## 基本使用

- 特点:能够直接把文件下载到沙盒中,我们需要做文件剪切处理(不会有内存飙升的问题)
- 缺点:无法监听文件的下载进度
- 应用:小文件下载

```objc
// 特点:能够直接把文件下载到沙盒中,我们需要做文件剪切处理(不会有内存飙升的问题)
// 缺点:无法监听文件的下载进度
// 应用:小文件下载
- (void) downloadFileDataWithBlock{

    // 1.确定 URL
    //    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_01.mp4"];
    NSURL *url = [NSURL URLWithString:@"http://img4.imgtn.bdimg.com/it/u=4022475582,4041976040&fm=23&gp=0.jpg"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 3.创建 session对象
    NSURLSession *session = [NSURLSession sharedSession];

    // 4.根据 session 创建 task
    /*
     第一个参on:位置,该文件保存到沙盒中的位置
     respon数:请求对象
     第二个参数:completionHandler 当下载结束(成功|失败)的时候调用
     locatise:响应头信息
     error:错误信息
     */
    NSURLSessionDownloadTask *downloadTask = [session downloadTaskWithRequest:request completionHandler:^(NSURL * _Nullable location, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        NSLog(@"location = %@", location);

        // 6.处理文件

        // 6.1获得文件名称
        NSString *fileName = response.suggestedFilename;

        // 6.2拼接文件路径
        NSString *fullPath = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:fileName];

        // 6.3执行剪切操作
        [[NSFileManager defaultManager] moveItemAtURL:location toURL:[NSURL fileURLWithPath:fullPath] error:nil];

        NSLog(@"fullPath = %@", fullPath);
    }];

    // 5.执行 task
    [downloadTask resume];
}
```
