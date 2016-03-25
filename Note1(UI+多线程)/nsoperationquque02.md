# 02 队列的暂停, 恢复, 取消

- 队列操作方法
    - -setSuspended: 是否暂停
    - -cancelAllOperations: 取消所有操作
- 注意:
    - 队列里面的任务是有状态的,不能暂停当前正在处于执行状态的操作
    - 不能取消当前正在处于执行状态的操作,只能取消后面处于等待状态的

```objc
// 暂停
- (IBAction)suspendButtonDidClicked:(id)sender {

    //暂停,可以恢复
    //队列里面的任务是有状态的,不能暂停当前正在处于执行状态的操作
    [self.queue setSuspended:YES];
}

// 恢复
- (IBAction)resumButtonDidClicked:(id)sender {

    [self.queue setSuspended:NO];
}

// 取消
- (IBAction)cancelButtonDidClicked:(id)sender {

    //取消所有的操作
    //不能取消当前正在处于执行状态的操作,只能取消后面处于等待状态的
    [self.queue cancelAllOperations];
}
```
