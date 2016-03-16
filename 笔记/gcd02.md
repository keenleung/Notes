##7.2 同步函数+并发队列

- 会不会开线程: 不会

- 如果开线程,那么开几条: 0

- 队列内部任务如何执行: 串行

```objc
// 情况三
// 同步函数+并发队列
// 会不会开线程:不会
// 如果开线程,那么开几条 0
// 队列内部任务如何执行:串行
- (void) syncConCurrent{

    // 1.创建并发队列
    dispatch_queue_t queue = dispatch_queue_create("com.keenleung.www", DISPATCH_QUEUE_CONCURRENT);

    NSLog(@"start...");

    // 2.使用同步函数添加操作到队列中
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
