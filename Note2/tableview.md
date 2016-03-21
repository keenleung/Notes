# tableView 显示网络数据注意点

##需要发送请求完成后刷新 UI

使用 SDWebImage 请求网络图片资源

```objc
@interface ViewController ()
// 数据
@property (nonatomic, strong) NSArray *videos;
@end
```
```objc
...
- (void)viewDidLoad {
    [super viewDidLoad];

    // 1.发送请求
    [[[NSURLSession sharedSession] dataTaskWithURL:[NSURL URLWithString:@"http://120.25.226.186:32812/video?type=JSON"] completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        // 2.解析数据(反序列化)
        id obj = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];

        NSArray *arr = obj[@"videos"];

        // 3.字典转模型(使用 MJExtension)
        self.videos = [Video mj_objectArrayWithKeyValuesArray:arr];

        // 5.刷新 UI
        /*
         为什么要刷新 UI ?
         跟踪程序执行的过程,发现: tableview 显示出来之后, 数据还没请求完毕, 即 self.videos 的数值, 在 tableview 显示出来之后, 还是空的, 要等网络请求完毕之后, 才会有值.
         所以需要在网络请求完毕之后, 刷新 tableview, 才能从内存中获取图片数值
         */
        dispatch_async(dispatch_get_main_queue(), ^{

            [self.tableView reloadData];
        });

    }] resume];

}
...
```
##过程分析
- 为什么要刷新 UI ?
        跟踪程序执行的过程,发现: tableview 显示出来之后, 数据还没请求完毕, 即 self.videos 的数值, 在 tableview 显示出来之后, 还是空的, 要等网络请求完毕之后, 才会有值.
        所以需要在网络请求完毕之后, 刷新 tableview, 才能从内存中获取图片数值
