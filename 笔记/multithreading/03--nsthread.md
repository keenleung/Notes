# 03 NSThread基本使用 - 掌握

##常用属性
- name : 线程的名称
- threadPriority : 线程的优先级,0.0~1.0 1.0是最高,默认是0.5

##常用方法
- -start : 执行
- +currentThread : 获取当前线程.number=1是主线程, 其他之外的, 都是子线程
- +detachNewThreadSelector... : 创建
- +sleepForTimeInterval : 阻塞线程多少秒, 后面参数是秒数
- +exit : 强制退出线程

##创建方式
####方式一: alloc initWithTarget...
```objc
// 方式一 alloc initWithTarget...
- (void) createNewThread1{

    //1.创建线程
    /*
     第一个参数:目标对象 self
     第二个参数:方法选择器 调用方法的名称
     第三个参数:调用方法的参数 没有着传值nil
     */
    ThreadA *threadA = [[ThreadA alloc] initWithTarget:self selector:@selector(run:) object:@"创建线程 A"];
    threadA.name = @"线程 A";
    // 设置线程的优先级
    threadA.threadPriority = 1.0; //0.0~1.0 1.0是最高,默认是0.5
    // 开始执行
    [threadA start];

    ThreadA *threadB = [[ThreadA alloc] initWithTarget:self selector:@selector(run:) object:@"创建线程 B"];
    threadB.name = @"线程 B";
    threadB.threadPriority = 0.1;
    [threadB start];

    ThreadA *threadC = [[ThreadA alloc] initWithTarget:self selector:@selector(run:) object:@"创建线程 C"];
    threadC.name = @"线程 C";
    threadC.threadPriority = 0.5;
    [threadC start];
}

//当线程中所有的任务都执行完毕的时候线程会被释放
- (void) run: (NSString *)param{

    for (int i = 0; i < 100; i++) {
        NSLog(@"index = %zd---run---%@---%@, %@", i, [NSThread currentThread].name, param, [NSThread currentThread]);
    }
}

```
==================================================================================

ThreadA.h
```objc
#import <Foundation/Foundation.h>

@interface ThreadA : NSThread

@end
```
ThreadA.m
```objc
#import "ThreadA.h"

@implementation ThreadA

- (void)dealloc{
    NSLog(@"%@----dealloc", [NSThread currentThread]);
}

@end
```

####方式二: detachNewThreadSelector..分离一条子线程
```objc
// 方式二 detachNewThreadSelector..分离一条子线程
- (void) createNewThread2{
    [NSThread detachNewThreadSelector:@selector(run:) toTarget:self withObject:@"分离一条子线程"];
}

//当线程中所有的任务都执行完毕的时候线程会被释放
- (void) run: (NSString *)param{

    for (int i = 0; i < 100; i++) {
        NSLog(@"index = %zd---run---%@---%@, %@", i, [NSThread currentThread].name, param, [NSThread currentThread]);
    }
}
```

####方式三: performSelectorInBackground...创建后台线程
```objc
// 方式三 performSelectorInBackground...创建后台线程
- (void)createNewThread3{
    [self performSelectorInBackground:@selector(run:) withObject:@"创建后台线程"];
}
```

#### 方式四: 自定义,alloc init

需重写 main 方法, 把需要的操作写到 main 方法里面

调用 start 方法后, 就会执行 main 方法

```objc
// 方式四 alloc init
// 这时候要重写 main 方法, 把需要的操作写到 main 方法里面
- (void)createNewThread4{
    [[[ThreadB alloc] init] start];
}

```

ThreadB.h
```objc
#import <Foundation/Foundation.h>

@interface ThreadB : NSThread
@end
```
ThreadB.m
```objc
#import "ThreadB.h"
@implementation ThreadB

- (void)main{
    NSLog(@"---main---%@", [NSThread currentThread]);
}
@end
```
