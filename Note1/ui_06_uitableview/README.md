##UITableView
####tableView 常用属性
- `rowHeight`: 行高
- `sectionHeaderHeight` 每组的组头标题的高度
- `separatorColor` 分割线的颜色
- `separatorStyle` 分割线的样式
- `tableHeaderView` 表头控件
- `tableFooterView` 表尾控件
- `sectionIndexColor` 字体导航栏的字体颜色
- `sectionIndexBackgroundColor` 字体导航栏的背景色

例子:
```objc
    // 每一行的高度
    self.tableView.rowHeight = 80;

    // 每组的组头标题的高度
    self.tableView.sectionHeaderHeight = 40;

    // 设置分割线的颜色
    self.tableView.separatorColor = [UIColor redColor];
    // 设置分割线的样式
    self.tableView.separatorStyle = UITableViewCellStyleSubtitle;

    // 设置表头控件
    //self.tableView.tableHeaderView = [UIButton buttonWithType:UIButtonTypeContactAdd];
    UIImage *image = [UIImage imageNamed:@"m_3_100"];
    UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
    self.tableView.tableHeaderView = imageView;
    // 设置表尾控件
    self.tableView.tableFooterView = [UIButton buttonWithType:UIButtonTypeInfoLight];

    // 导航条
    self.tableView.sectionIndexColor = [UIColor whiteColor];
    self.tableView.sectionIndexBackgroundColor = [UIColor orangeColor];
```



####tableViewCell 常用属性
- `imageView` 图片
- `textLabel` 文字显示
- `detailTextLabel` 说明显示
- `accessoryType` 右边显示的指示样式类型
- `accessoryView` 右边显示的指示样式的视图
- `selectionStyle` 选中时的样式

例子:
```objc
// UITableViewCell
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    // 1.获取可重用的 cell
    static NSString *reuseId = @"group";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:reuseId];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseId];

        // cell 重用的时候,公共的部分写在这里面
        cell.backgroundColor = [UIColor grayColor];

        // 右边显示的指示样式
        //cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;
        cell.accessoryView = [[UISwitch alloc] init];
    }

    // 不是公共的部分,写出来,但要用 if 和 else 区分开
    // 设置选中时的样式
    if (indexPath.row % 2 == 0) {
        cell.selectionStyle = UITableViewCellStyleSubtitle;
    }else{
        cell.selectionStyle = UITableViewCellStyleDefault;
    }

    // 2.给子控件赋值
    CarGroup *carGroup = self.carGroups[indexPath.section];
    Car *car = carGroup.cars[indexPath.row];
    cell.imageView.image = [UIImage imageNamed:car.icon];
    cell.textLabel.text = car.name;

    // 3.返回 cell
    return cell;
}
```

#### cell 重用的两种方式
注意:
```
方法-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;如果返回 nil, 则会报错!
```
__方式一__:

```objc
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{

    // 1.获取可重用的 cell
    static NSString *reuseId = @"group";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:reuseId];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseId];

        ...
        //(1)公共部分
    }

    //(2)不相同的部分

    // 2.给子控件赋值
    ...
    cell.imageView.image = .. ;
    cell.textLabel.text = .. ;
    ...

    // 3.返回 cell
    return cell;
}
```

注意:
为了防止 cell 重用的过程中,数据或样式出现问题,需要把__`公共`__的部分写在`(1)`, 把__`不相同`__的部分写在`(2)`那里, 并且要用 if 和 else 区分开来,如:
```objc
if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseId];

        // cell 重用的时候,公共的部分写在这里面
        cell.backgroundColor = [UIColor grayColor];
        // 右边显示的指示样式
        cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;
    }

    // 不是公共的部分,写出来,但要用 if 和 else 区分开
    if (indexPath.row % 2 == 0) {
        cell.selectionStyle = UITableViewCellStyleSubtitle;
    }else{
        cell.selectionStyle = UITableViewCellStyleDefault;
    }
```

<br />
<br />
__方式二: 注册__registerClass
<br />
<br />
一般应用到自定义 cell 上:
- 注册 id
```objc
// 注册 ID这个标识 对应的 cell类型 为UITableViewCell这种类型
[self.tableView registerClass:[XMGTestCell class] forCellReuseIdentifier:ID];
```

- 从缓存池中获取可重用的 cell

```objc
 XMGTestCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];
```

过程说明:<br />
```
从缓存池中取 标识为 ID 的 cell 时,会判断缓存池中有没有.
若有,则获取.
若没有,则判断 ID 这个标识是否注册了某种 UITableViewCell类型,有的话,则创建该类的实体对象;没有的话,则返回 nil
```


