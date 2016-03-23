# MIMEType获取

- 获得文件MIMEType的几种方法
 - 对着该文件发一个请求,得到响应头信息
 - 调用C语言的API
 - 百度
 - 通用的二进制数据格式  `application/octet-stream`

#### 方式一: 对着该文件发一个请求, 取得响应头信息
```objc
// 对着该文件发一个请求, 取得响应头信息
- (void) getMIMEType{

    [[[NSURLSession sharedSession] dataTaskWithURL:[NSURL fileURLWithPath:@"/Users/apple/Desktop/300-2.jpeg"] completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        NSLog(@"%@", response.MIMEType);

    }] resume];
}
```


#### 方式二: 调用C语言的API

需要导入头文件
`#import <MobileCoreServices/MobileCoreServices.h>`

```objc
// 方式二: 调用C语言的API
/**
 *  @param path 文件路径
 */
-(NSString *)mimeTypeForFileAtPath:(NSString *)path
{
    if (![[[NSFileManager alloc] init] fileExistsAtPath:path]) {
        return nil;
    }

    CFStringRef UTI = UTTypeCreatePreferredIdentifierForTag(kUTTagClassFilenameExtension, (__bridge CFStringRef)[path pathExtension], NULL);
    CFStringRef MIMEType = UTTypeCopyPreferredTagWithClass (UTI, kUTTagClassMIMEType);
    CFRelease(UTI);
    if (!MIMEType) {
        return @"application/octet-stream";
    }
    return (__bridge NSString *)(MIMEType);
}
```
