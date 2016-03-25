# 01  设置队列的最大并发数

####maxConcurrentOperationCount
- 设置最大并发数:同一时间可以处理多少个操作

- maxConcurrentOperationCount:
    - 当maxConcurrentOperatdionCount == 1的时候是串行队列,默认是并发队列
    -  maxConcurrentOperationCount == -1 默认值(最大值~不受限制)

```objc
// 最大并发数
// 串行 or 并发
- (void) maxConcurrentCount{

    // 1.创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    // 2.设置最大并发数
    // 设置最大并发数:同一时间可以处理多少个操作
    // maxConcurrentOperationCount
    // 当maxConcurrentOperatdionCount == 1的时候是串行队列,默认是并发队列
    // maxConcurrentOperationCount == -1 默认值(最大值~不受限制)
    queue.maxConcurrentOperationCount = 1;

    // 3.封装操作
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"1 ---- %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"2 ---- %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"3 ---- %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op4 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"4 ---- %@", [NSThread currentThread]);
    }];

    // 3.1 要设置队列如果是串行队列的时候:
    //     注意不能使用追加操作的方法
    // 添加的队列, 执行次序不确定
    /*
    [op3 addExecutionBlock:^{
        NSLog(@"3.1 ---- %@", [NSThread currentThread]);
    }];
    [op3 addExecutionBlock:^{
        NSLog(@"3.2 ---- %@", [NSThread currentThread]);
    }];
    */

    // 4.把操作放到队列中
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
    [queue addOperation:op4];
}

```
