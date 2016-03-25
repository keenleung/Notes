# AFN序列化

- 一般过程
    - 创建会话管理者
    ```objc
    // 1.创建会话管理者
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    ```
    - 设置参数列表
    ```objc
    // 设置请求参数列表
    NSDictionary *dict = @{
                           @"username":@"520it",
                           @"pwd":@"520it",
                           @"type":@"JSON"
                           };
    ```
    - 发送请求


- NSProgress 对象常用属性
    - completedUnitCount : 已经接收的字节
    - totalUnitCount : 总字节数
    - suggestedFilename : 文件名

- NSMutableURLRequest 常用属性和方法
    ```objc
    // 修改发送请求的方式
    request.HTTPMethod = @"POST";
    ```
    ```objc
    // 5.设置请求头信息
    NSString *header = [NSString stringWithFormat:@"multipart/form-data; boundary=%@",kboundary];
    [request setValue:header forHTTPHeaderField:@"Content-Type"];
    ```
