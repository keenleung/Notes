# Base64编码和解码(了解)


#### base64编码
```objc
//base64编码
-(NSString *)base64Encoding:(NSString *)string
{
    //1.把字符串转换为二进制数据
    NSData *data = [string dataUsingEncoding:NSUTF8StringEncoding];

    //2.base64编码,编码之后把字符串返回
    return [data base64EncodedStringWithOptions:0];
}
```

#### base64解码
```objc
//base64解码
-(NSString *)base64DeCoding:(NSString *)string
{
    //1.转换为二进制数据(在该过程中就已经完成了解码操作)
    NSData *data = [[NSData alloc]initWithBase64EncodedString:string options:0];

    //2.把二进制数据转换为字符串
    return [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
}
```
