# NSURLConnection-发送 POST 请求

##基本过程

- 注意点:
    - NSMutableURLRequest对象常用属性:
        - HTTPMethod : 设置请求方式
        - timeoutInterval : 设置请求超时
        - HTTPBody : 设置请求体

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login"];

    // 2.创建请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 3.修改请求方法(POST)需要大写
    request.HTTPMethod = @"POST";

    // 3.1设置请求超时
    request.timeoutInterval = 15;

    // 3.2设置请求头信息
    // 可从浏览器的开发者模式下面查看, forHTTPHeaderField 的值必须要大小写一致
    [request setValue:@"iOS 10.0" forHTTPHeaderField:@"User-Agent"];

    // 4.设置请求体
    request.HTTPBody = [@"username=520it&pwd=520it" dataUsingEncoding:NSUTF8StringEncoding];

    // 5.发送请求
    [NSURLConnection sendAsynchronousRequest:request queue:[[NSOperationQueue alloc] init] completionHandler:^(NSURLResponse * _Nullable response, NSData * _Nullable data, NSError * _Nullable connectionError) {

        // 6.解析数据
        if (connectionError) {
            NSLog(@"%@", connectionError);
        }else{
            NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
            NSLog(@"%@", [NSThread currentThread]);
        }
    }];
}

```
