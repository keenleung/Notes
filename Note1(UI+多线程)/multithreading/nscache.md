#16 NSCache简单介绍
##1. NSCache简单说明
- NSCache是苹果官方提供的缓存类，具体使用和NSMutableDictionary类似，在AFN和SDWebImage框架中被使用来管理缓存
- 苹果官方解释NSCache在系统内存很低时，会自动释放对象（但模拟器演示不会释放）
    - 建议：接收到内存警告时主动调用removeAllObject方法释放对象
- NSCache是线程安全的，在多线程操作中，不需要对NSCache加锁
- NSCache的Key只是对对象进行Strong引用，不是拷贝，在清理的时候计算的是实际大小而不是引用的大小

##2. NSCache属性和方法介绍
- 属性介绍
    - name:名称
    - delegete:设置代理
    - totalCostLimit：缓存空间的最大总成本，超出上限会自动回收对象。默认值为0，表示没有限制
    - countLimit：能够缓存的对象的最大数量。默认值为0，表示没有限制
    - evictsObjectsWithDiscardedContent：标识缓存是否回收废弃的内容
- 方法介绍
    - -(void)setObject:(ObjectType)obj forKey:(KeyType)key;//在缓存中设置指定键名对应的值，0成本
    - -(void)setObject:(ObjectType)obj forKey:(KeyType)keycost:(NSUInteger)g;
            在缓存中设置指定键名对应的值，并且指定该键值对的成本，用于计算记录在缓存中的所有对象的总成本
            当出现内存警告或者超出缓存总成本上限的时候，缓存会开启一个回收过程，删除部分元素
    - -(void)removeObjectForKey:(KeyType)key;
            删除缓存中指定键名的对象
    - -(void)removeAllObjects;
            删除缓存中所有的对象

##3. 示例代码
```objc
#import "ViewController.h"

#define CacheObjNumber 10
#define CacheCostNumber 2

@interface ViewController ()<NSCacheDelegate>

// 缓存
@property (nonatomic, strong) NSCache *cache;

@end

@implementation ViewController

// 懒加载
- (NSCache *)cache{
    if (!_cache) {
        _cache = [[NSCache alloc] init];

        // 设置成本限制
        // 缓存空间的最大总成本，超出上限会自动回收对象。默认值为0，表示没有限制
        _cache.totalCostLimit = 10;

        // 设置能够缓存的对象的最大数量
        // 能够缓存的对象的最大数量。默认值为0，表示没有限制
        _cache.countLimit = 8;

        // 设置代理,监听回收过程
        _cache.delegate = self;
    }
    return _cache;
}


// 存
- (IBAction)saveButtonDidClicked:(id)sender {

    for (int i = 0; i<CacheObjNumber; i++) {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"Snip20160316_21.png" ofType:nil];
        NSData *data = [NSData dataWithContentsOfFile:path];

        //[self.cache setObject:data forKey:@(i)];
        [self.cache setObject:data forKey:@(i) cost:CacheCostNumber];

        NSLog(@"存数据 ... %i", i);
    }
    NSLog(@"++++++++++++++++++++++++");
}

// 取
- (IBAction)getButtonDidClicked:(id)sender {

    // 检查缓存
    for (int i = 0; i<CacheObjNumber; i++) {

        NSData *data = [self.cache objectForKey:@(i)];
        if (data) {
            NSLog(@"取数据 ... %i", i);
        }
    }
}



#pragma mark - NSCacheDelegate
// 当开启回收过程的时候回调用
// 回收数据的时候, 是从最旧的数据开始
- (void)cache:(NSCache *)cache willEvictObject:(id)obj{

    NSLog(@"开启回收 --- %zd, %@", [obj length], [obj class]);
}

@end
```
===========================================================================================
