# NSURLSession的基本使用

- 注意
    - 一般过程
        - 1.确定 URL
        - 2.创建可变的请求对象
        - 3.获得会话对象
        - 4.根据会话对象创建 task
        - 5.执行 task
        - 6.解析数据
    - 会话对象的获取 : (这是一个单例对象)
        - NSURLSession *session = [NSURLSession sharedSession];
    - 根据会话对象创建 task
        - 创建方式有
            - dataTaskWithRequest:request
            - dataTaskWithURL:url
        - completionHandler : 该block块在子线程中调用
    - 任务的执行:
        - 调用 -resume 方法
    - 关于task
        - NSURLSessionTask是一个抽象类，本身不能使用，只能使用它的子类
            - NSURLSessionDataTask
            - NSURLSessionUploadTask
            - NSURLSessionDownloadTask

## 1.发送 GET 请求
####方式一 : dataTaskWithRequest
```objc
// dataTaskWithRequest
- (void) get1{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 3.获得会话对象
    NSURLSession *session = [NSURLSession sharedSession];

    // 4.根据会话对象创建 task
    /*
     第一个参数:请求对象
     第二个参数:completionHandler 请求结束的时候调用
     data:响应体信息
     response:响应头信息
     error:错误信息

     注意:completionHandler:该block块在子线程中调用
     */
    NSURLSessionDataTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        // 这大括号里的以操作都是在子线程中执行的
        // 6.解析数据
        NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
        NSLog(@"%@", [NSThread currentThread]);
    }];

    // 5.执行 task
    [task resume];
}
```

####方式二 : dataTaskWithURL
```objc
// dataTaskWithURL
- (void) get2{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.获得会话对象
    NSURLSession *session = [NSURLSession sharedSession];

    // 3.根据会话对象, 创建 task
    // 该方法内部会自动把url包装成一个请求对象--以 GET 方式请求
    NSURLSessionDataTask *task = [session dataTaskWithURL:url completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        // 5.解析数据
        if (error) {

            NSLog(@"%@", error);

        }else{

            NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
            NSLog(@"%@", [NSThread currentThread]);

        }
    }];

    // 4.执行 task
    [task resume];
}
```

##2.发送 POST 请求
```objc
- (void) post{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 2.1设置请求方式
    request.HTTPMethod = @"POST";

    // 2.2设置请求体
    request.HTTPBody = [@"username=520it&pwd=520it&type=JSON" dataUsingEncoding:NSUTF8StringEncoding];

    // 3.获得会话对象
    NSURLSession *session = [NSURLSession sharedSession];

    // 4.根据会话对象, 创建task
    NSURLSessionDataTask *task =[session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        // 6.解析数据
        if (error) {

            NSLog(@"%@", error);

        }else{

            NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
            NSLog(@"%@", [NSThread currentThread]);

        }
    }];

    // 5.执行 task
    [task resume];
}
```
