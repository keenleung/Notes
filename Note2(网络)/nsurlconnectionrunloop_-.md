# NSURLConnection和Runloop补充(以代理方式发送请求) - 掌握

##1.在主线程中调用

立即执行 和 非立即执行 的方式, 都没什么问题

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{

    //[self delegate1];

    [self delegate2];
}
```

- 立即执行

```objc
- (void) delegate1{

    NSLog(@"%s --- %@", __func__, [NSThread currentThread]);

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL: url];

    // 3.设置代理
    NSURLConnection *connect = [[NSURLConnection alloc] initWithRequest:request delegate:self];

}
```

- 手动开启

```objc
- (void) delegate2{

    NSLog(@"%s --- %@", __func__, [NSThread currentThread]);

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 3.设置代理
    NSURLConnection *connect = [[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:NO];


    // 4.手动请求
    // 注意:
    // 1)该方法内部会把connect对象作为一个source添加到runloop中,并且制定运行模式为默认
    // 2)如果发现当前的runloop不存在,那么该方法内部会自动的创建并开启当前子线程的runloop (解析了:为什么在这里不需要给子线程创建 runloop)
    [connect start];
}
```

- 代理方法 NSURLConnectionDataDelegate

```objc
#pragma mark - NSURLConnectionDataDelegate
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response{

    NSLog(@"%s---%@", __func__, [NSThread currentThread]);
}

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data{

    NSLog(@"%s --- %@", __func__, [NSThread currentThread]);
}
```

##2. 如果一开始, 方法调用是在子线程
```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{

    // 开启子线程调用
    //[NSThread detachNewThreadSelector:@selector(delegate1) toTarget:self withObject:nil];

    [NSThread detachNewThreadSelector:@selector(delegate2) toTarget:self withObject:nil];
}
```

- 问题:
    - 立即执行的方式, 需要添加 runloop 之后, 才会发送请求
    - 非立即执行的方式, 无需添加 runloop, 也能正常发送请求
- 解析:
    - 前提(需要知道的是): 发送请求的操作, 是需要放在 runloop 中执行的
    - 立即执行的方式: delegate1 方法的调用是在子线程, 子线程的 runloop 需要手动开启才有
    - 非立即执行的方式: 虽然 delegate2 方法的调用是在子线程, 但是 NSURLConnection 对象的 start 方法的执行, 会默认帮我们开启 runloop. 如果发现当前的runloop不存在,那么该方法内部会自动的创建并开启当前子线程的runloop
- 注意点:
    - 设置代理的调用线程
        - [connect setDelegateQueue:[[NSOperationQueue alloc] init]];

- 立即执行: 需要添加 runloop

```objc
- (void) delegate1{

    NSLog(@"%s --- %@", __func__, [NSThread currentThread]);

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL: url];

    // 为子线程创建 runloop
    NSRunLoop *current = [NSRunLoop currentRunLoop];

    // 3.设置代理
    NSURLConnection *connect = [[NSURLConnection alloc] initWithRequest:request delegate:self];

    // 开启 runloop
    [current run];

    // 4.设置代理方法 的 调用线程(队列)
    // 可设置 代理方法 是在主线程调用还是在子线程
    [connect setDelegateQueue:[[NSOperationQueue alloc] init]];
}
```

- 非立即执行: 无需添加 runloop

```objc
- (void) delegate2{

    NSLog(@"%s --- %@", __func__, [NSThread currentThread]);

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=520it&pwd=520it&type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

    // 3.设置代理
    NSURLConnection *connect = [[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:NO];

    // 设置代理方法的调用线程
    [connect setDelegateQueue:[[NSOperationQueue alloc] init]];

    // 4.手动请求
    // 注意:
    // 1)该方法内部会把connect对象作为一个source添加到runloop中,并且制定运行模式为默认
    // 2)如果发现当前的runloop不存在,那么该方法内部会自动的创建并开启当前子线程的runloop (解析了:为什么在这里不需要给子线程创建 runloop)
    [connect start];
}
```
