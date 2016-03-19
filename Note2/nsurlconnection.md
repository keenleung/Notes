# NSURLConnection 发送 GET 请求

## 场景一: 发送同步请求

- 注意点:
    - 同步执行, 如果没有得到服务器返回的数据, 则会发生阻塞

```objc
- (void) sendBySync{

    // 1.确定请求路径
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建请求对象
    // 请求对象里面默认已经包含了请求头信息+URL+请求方法(默认GET)
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    // 3.1 响应头信息
    //NSHTTPURLResponse :真实类型
    //NSURLResponse *response = nil;
    NSHTTPURLResponse *response = nil;

    // 3.2 错误信息
    NSError *error = nil;

    // 3.发送请求
    /*
     第一个参数:请求对象
     第二个参数:响应头信息
     第三个参数:错误信息,如过请求失败那么error有值

     注意:
     同步:阻塞(如果没有得到服务器返回的数据那么该方法将不会被过掉)
     */
    NSData *data = [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:&error];

    if (response) {
        NSLog(@"%@", response.allHeaderFields);
    }

    if (error) {
        NSLog(@"%@", error);
    }

    // 4.解析数据
    NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

}
```

## 场景二: 发送异步请求(方式一: 非代理)
```objc
- (void) sendByAsync{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    // 3.发送异步请求
    /*
     第一个参数:请求对象
     第二个参数:操作队列 决定线程(决定的并不是整个方法的调用线程,而是completionHandler的调用线程)
     第三个参数:completionHandler 当请求结束(成功|失败)的时候调用
     response:响应头信息
     data:响应体信息
     connectionError:错误信息
     */
    [NSURLConnection sendAsynchronousRequest:request queue:[[NSOperationQueue alloc] init] completionHandler:^(NSURLResponse * _Nullable response, NSData * _Nullable data, NSError * _Nullable connectionError) {

        // 4.解析数据
        NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
        NSLog(@"%@", [NSThread currentThread]);

        if (connectionError) {
            NSLog(@"%@", connectionError);
        }
    }];
}
```

## 场景三: 发送异步请求(方式二: 代理)

- 注意:
    - 需要遵循 NSURLConnectionDataDelegate 协议

```objc
- (void) sendAsyncByDelegate{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    // 3.设置代理

    // 第一种设置代理的方法,内部会自动的发送请求
    //[NSURLConnection connectionWithRequest:request delegate:self];

    // 第二种设置代理的方法,内部会自动的发送请求
    //[[NSURLConnection alloc] initWithRequest:request delegate:self];

    // 第三种设置代理的方法
    // startImmediately:设置是否马上发送网络请求(YES or NO)
    // startImmediately 为 YES 的时候, 和上面两种没什么区别
    // startImmediately 为 NO 的时候, 需要手动发送请求 -> 调用 start 方法
    //
    //[[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:YES];
    NSURLConnection *connect = [[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:NO];
    // 手动发送请求
    [connect start];

    // 撤销请求
    //[connect cancel];

    NSLog(@"++++");
}
```

##常用代理方法(NSURLConnectionDataDelegate)
```objc
// 1.接收到服务器响应的时候调用
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response{

    NSLog(@"%s", __func__);

    self.responseData = [NSMutableData data];
}

// 2.接收到服务器返回数据的时候调用
// 如果数据较大那么该方法会调用多次
- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data{

    NSLog(@"%s --- %zd", __func__, [data length]);

    //拼接数据
    [self.responseData appendData:data]; // responseData 类型为 NSMutableData
}

// 3.当请求失败的时候调用(参数错误|接口错误|请求超时)
- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error{

    NSLog(@"%s", __func__);
}

// 4.当请求完成的时候调用
- (void)connectionDidFinishLoading:(NSURLConnection *)connection{

    NSLog(@"%s", __func__);

    NSLog(@"%@", [[NSString alloc] initWithData:self.responseData encoding:NSUTF8StringEncoding]);
}
```

