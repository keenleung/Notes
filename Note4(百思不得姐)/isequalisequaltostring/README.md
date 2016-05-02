## isEqual和isEqualToString
###1.区别
- `isEqualToString`
    - 比较两个字符串的内容
- `isEqual`
    - 默认情况下是比较两个对象的内存地址
    - 有一些系统自带的类(比如Foundation中的NSString,NSArray)重写了这个方法，改变了这个方法的判断规则(`一般改为比较两个对象的内容，不是内存地址`)
- `hash`
    - 一旦重写了isEqual:方法，最好重写hash方法，而且要遵守以下原则：
        - 1.isEqual:返回YES的2个对象，hash值一定要一样
        - 2.hash值一样的2个对象，isEqual:返回不一定是YES
        - 一般写法:  属性值相加, 如果是基本属性, 则直接相加; 如果是对象属性, 则取其 hash 再相加
        ```objc
            - (NSUInteger)hash
            {
            return self.age + self.no + self.name.hash + self.car.hash;
            }
        ```


一般数组操作:
- `containsObject`
    ```objc
    [array containsObject:p]
    ```
    - 遍历 array数组中的每个元素,然后让元素和 p 进行 isEqual 操作,如果匹配得到,则立即返回 true, 全部都没有匹配得到,则返回 false
- `indexOfObject`
    ```objc
    [array indexOfObject:string]
    ```
    - 遍历 array数组中的每个元素,然后让元素和 string 进行 isEqual 操作,如果匹配得到,则立即返回元素的 下标, 全部都没有匹配,则返回  `NSNotFound`.

###2.例子
```objc
- (void)test1
{
    NSString *string1 = @"jack";
    NSString *string2 = [NSString stringWithFormat:@"jack"];

    NSLog(@"%p %p", string1, string2);
    NSLog(@"string1 == string2 -> %zd", string1 == string2); // 0
    NSLog(@"[string1 isEqualToString:string2] -> %zd", [string1 isEqualToString:string2]); // 1
    NSLog(@"[string1 isEqual:string2] -> %zd", [string1 isEqual:string2]); // 1
}

- (void)test2
{
    NSString *string1 = @"jack";
    NSString *string2 = @"jack";

    NSLog(@"%p %p", string1, string2);
    NSLog(@"string1 == string2 -> %zd", string1 == string2); // 1
    NSLog(@"[string1 isEqualToString:string2] -> %zd", [string1 isEqualToString:string2]); // 1
    NSLog(@"[string1 isEqual:string2] -> %zd", [string1 isEqual:string2]); // 1
}

- (void)test5
{
    NSString *string1 = [NSString stringWithFormat:@"111"];
    NSString *string2 = [NSString stringWithFormat:@"222"];

    NSArray *array1 = @[string1, @"222", @"333"];
    NSArray *array2 = @[@"111", string2, @"333"];

    NSArray *array = @[array1, array2];

    NSLog(@"%zd", [array indexOfObject:array2]); // 0
}
```
