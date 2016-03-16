# 03 设置操作依赖

- 操作调用方法
    - -ddOperation: 添加操作依赖
        - 注意点:不能设置循环依赖
        - 设置循环依赖的结果是: 两者都不会执行
    - -completionBlock: 操作完成时调用

```objc
// 设置操作依赖
- (void) test3{

    // 1.创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    // 2.封装操作
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        for (int i = 0; i<5; i++) {
            NSLog(@"1 --- %zd --- %@", i, [NSThread currentThread]);
        }
    }];
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        for (int i = 0; i<5; i++) {
            NSLog(@"2 --- %zd --- %@", i, [NSThread currentThread]);
        }
    }];
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"3 --- %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op4 = [NSBlockOperation blockOperationWithBlock:^{
        for (int i = 0; i<5; i++) {
            NSLog(@"4 --- %zd --- %@", i, [NSThread currentThread]);
        }
    }];

    // 3.添加操作监听
    op4.completionBlock = ^(){
        NSLog(@"操作4已经完成了, 完成了, 完成了!!!");
    };

    // 4.设置操作依赖
    //设置操作依赖
    //注意点:不能设置循环依赖
    // 设置循环依赖的结果是: 两者都不会执行
    [op1 addDependency:op4];
    //[op4 addDependency:op1];

    [op4 addDependency:op2];
    [op2 addDependency:op3];

    // 依赖顺序:1 -> 4 -> 2 -> 3
    // 执行顺序:3 -> 2 -> 4 -> 1

    // 5.把操作放到队列中
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
    [queue addOperation:op4];
}

```
