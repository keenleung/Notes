# JSON解析
##1 .JSON简单介绍
- 1) 什么是JSON

        （1）JSON是一种轻量级的数据格式，一般用于数据交互
        （2）服务器返回给客户端的数据，一般都是JSON格式或者XML格式（文件下载除外）

- 2） 相关说明
        （1）JSON的格式很像OC中的字典和数组
        （2）标准JSON格式key必须是双引号
- 3）JSON解析方案
        a.第三方框架  JSONKit\SBJSON\TouchJSON
        b.苹果原生（NSJSONSerialization）

- 4)概念
        序列化 :(Serialization)将对象的状态信息转换为可以存储或传输的形式的过程。

## 2. 使用

#### 序列化 OC -> JSON

- 注意
    - 方法
    ```objc
    [NSJSONSerialization dataWithJSONObject:obj options:kNilOptions error:nil];
    ```
    - 是否能转成功的条件:
        - 1)最外层必须是数组或者是字典
        - 2)所有的对象必须是NSString, NSNumber, NSArray, NSDictionary, or NSNull
        - 3)字典里面所有的key都必须是字符串
        - 4)NSNumber必须是正规的而且不能死无穷大
    - 判断方法
        - [NSJSONSerialization isValidJSONObject: obj]


```objc
// 序列化
- (void) ocToJson{

    // 1.oc
    NSDictionary *obj = @{@"name" : @"keen", @"age" : @24};

    // 2.判断是否能够转成 JSON
    /*
     是否能转成功的条件:

     - Top level object is an NSArray or NSDictionary
     1)最外层必须是数组或者是字典
     - All objects are NSString, NSNumber, NSArray, NSDictionary, or NSNull
     2)所有的对象必须是NSString, NSNumber, NSArray, NSDictionary, or NSNull
     - All dictionary keys are NSStrings
     3)字典里面所有的key都必须是字符串
     - NSNumbers are not NaN or infinity
     4)NSNumber必须是正规的而且不能死无穷大
     */
    if ([NSJSONSerialization isValidJSONObject:obj]) {

        /*
         第一个参数:要转换的OC对象
         第二个参数:选项 NSJSONWritingPrettyPrinted 排版使用
         第三个参数:错误信息
         */
        NSData *data = [NSJSONSerialization dataWithJSONObject:obj options:kNilOptions error:nil];

        // 打印 JSON 数据
        NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

    }else{
        NSLog(@"该数据类型不支持转换为JSON");
    }
}
```

#### 反序列化 JSON -> OC

- 注意:
    - 调用方法
    ```objc
    [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];
    ```

```objc
// 反序列化
- (void) jsonToOC{

    // 1.确定 URL
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/video?type=JSON"];

    // 2.创建可变的请求对象
    NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:url];

    // 3.获得 session 对象(单例)
    NSURLSession *session = [NSURLSession sharedSession];

    // 4.根据 session 创建 task
    NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        // json 转 OC
        /*
         第一个参数:要解析的数据
         第二个参数:选项
         第三个参数:错误信息

         options 说明:
         NSJSONReadingMutableContainers = (1UL << 0), 得到的字典或者是数组是可变的
         NSJSONReadingMutableLeaves = (1UL << 1),     内部的字符串也是可变的
         NSJSONReadingAllowFragments = (1UL << 2)     得到的数据类型最外层既不是字典也不是数组
         0
         */
        id obj = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];

        NSLog(@"%@ --- %@", [obj class], obj);
    }];

    // 5.执行
    [dataTask resume];
}
```

##3. 如何查看复杂的JSON数据
- 方法一：
        在线格式化http://tool.oschina.net/codeformat/json
- 方法二：
        把解析后的数据写plist文件，通过plist文件可以直观的查看JSON的层次结构。
        [dictM writeToFile:@"/Users/keen/Desktop/videos.plist" atomically:YES];

