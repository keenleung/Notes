##7.1.同步函数+串行队列

- 会不会开线程:不会

- 如果开线程,那么开几条: 0

- 队列内部任务如何执行:串行

```objc
// 情况一
// 同步函数+串行队列
// 会不会开线程:不会
// 如果开线程,那么开几条: 0
// 队列内部任务如何执行:串行
- (void) syncSerial{

    // 1.创建串行队列
    dispatch_queue_t queue = dispatch_queue_create("com.keenleung.wwww", DISPATCH_QUEUE_SERIAL);

    NSLog(@"start...");

    // 2.使用同步函数添加操作到队列中
    // 该方法完成了以下操作:1)封装任务 2)把任务添加到队列
    dispatch_sync(queue, ^{
        NSLog(@"1----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"2----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"3----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"4----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"5----%@", [NSThread currentThread]);
    });

    NSLog(@"end...");
}
```


####2.同步函数+串行队列(死锁)
```objc
// 情况二
// 同步函数+串行 (特殊)
// 会不会开线程:不会
// 如果开线程,那么开几条: 0
// 队列内部任务如何执行:串行
- (void) syncSerial2{

    // 1.创建串行队列
    dispatch_queue_t queue = dispatch_queue_create("com.keenleung.wwww", DISPATCH_QUEUE_SERIAL);

    NSLog(@"start...");

    // 2.使用同步函数添加操作到队列中
    // 该方法完成了以下操作:1)封装任务 2)把任务添加到队列
    dispatch_sync(queue, ^{
        NSLog(@"1----%@", [NSThread currentThread]);

        // 发生死锁
        dispatch_sync(queue, ^{
            NSLog(@"+++++++++");
        });
    });

    dispatch_sync(queue, ^{
        NSLog(@"2----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"3----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"4----%@", [NSThread currentThread]);
    });
    dispatch_sync(queue, ^{
        NSLog(@"5----%@", [NSThread currentThread]);
    });

    NSLog(@"end...");
}
```
