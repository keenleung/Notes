##UIPickerView

- 数据源方法 UIPickerViewDataSource

        设置有多少组和每组有多少行
    - -(`NSInteger`) `numberOfComponentsInPickerView`: (UIPickerView *)pickerView

            返回有多少组,即列数
    - -(`NSInteger`) `pickerView`: (UIPickerView *)pickerView   `numberOfRowsInComponent`:(NSInteger)component
            返回每组有多少行

- 代理方法 UIPickerViewDelegate
        设置每一行显示的方式,比如,显示文字或显示一个控件; 监听每组选中的行等.
    - -(`NSString *`)pickerView: titleForRow: forComponent:
            给每一行返回一个字符串
    - -(`UIView *`)pickerView: viewForRow: forComponent: reusingView:
            给每一行返回一个 View
比如:
```objc
#pragma mark - UIPickerView 的代理方法
-(UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(UIView *)view{

    // 如果有重用的 view, 会传一个 view 进来
    FlagView *flagView = (FlagView *)view;
    if (!flagView) {
        flagView = [FlagView flagView];
    }

    // 传递数据
    Flag *flag = self.flags[row];
    flagView.flag = flag;

    // 设置 frame
    flagView.bounds = CGRectMake(-50, 0, 200, 0);

    flagView.backgroundColor = [UIColor grayColor];

    return flagView;
}
```

    - -(`void`) pickerView: didSelectRow: inComponent:
            监听每组选中的行,根据返回 行与组 的数据,获取或更改界面数据
比如:可以设置选中 (i+1,i>=0) 组的第一行
```objc
[self pickerView:self.pickerView didSelectRow:0 inComponent:i];
```
    - -(`CGFloat`) pickerView: (UIPickerView *)pickerView  `rowHeightForComponent`: (NSInteger)component
            返回行高

- 常用方法
    - selectedRowInComponent:
            获取指定组选择的行的下标
比如: 实现随机获取每组的数据,但不能与之前的重复
```objc
// 总列数
    NSInteger column = self.foods.count;
    for (NSInteger i= 0; i< column; i++) {
        // 每一列共有多少行数据
        NSArray *items = self.foods[i];
        NSInteger rowInColumn = items.count;
        // 在行范围内产生随机数
        NSInteger randomRow = arc4random_uniform((int)rowInColumn);

        // 每列选取的,不能与之前的一样
        // 获取旧的数据
        NSInteger oldRow = [self.pickerView selectedRowInComponent:i];
        while (randomRow == oldRow) {
            randomRow = arc4random_uniform((int)rowInColumn);
        }

        // 更改 label 的数据
        [self pickerView:self.pickerView didSelectRow:randomRow inComponent:i];
        // 更改 pickerView 中的数据
        [self.pickerView selectRow:randomRow inComponent:i animated:YES];
    }
```
    - reloadComponent
            实现部分刷新
    比如:
    ```objc
    #pragma mark - 选中某一行
-(void) pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component{
    if (component == 0) {
        // 更新省份索引
        self.indexOfProvince = row;

        // 部分刷新
        [pickerView reloadComponent:1];

        // 不管省份选取的是哪一个,城市显示的都是第一行
        //[self pickerView:self.pickerView didSelectRow:0 inComponent:1];
        [pickerView selectRow:0 inComponent:1 animated:YES];
    }
}
```

##UIDatePicker
- 获取系统本地化方式
        [NSLocale availableLocaleIdentifiers]
- 常用属性或方法
```objc
 UIDatePicker *datePicker = [[UIDatePicker alloc] init];
    self.datePicker = datePicker;
    // 以当地时间方式显示
    datePicker.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"zh"];// 中国的本地化标识是: zh
    // 设置显示的时间格式
    datePicker.datePickerMode = UIDatePickerModeDate;// 年月日
//    datePicker.datePickerMode = UIDatePickerModeDateAndTime;年月日时分秒
```
- 案例: 选取选中的时间

```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    // 打印当前可用的本地化标识
    NSLog(@"%@",[NSLocale availableLocaleIdentifiers]);

    /****** UITextField */
    UITextField *textField = [[UITextField alloc] init];
    self.textField = textField;
    [self.view addSubview:textField];
    // 设置 frame
    textField.frame = CGRectMake(0, 40, self.view.bounds.size.width, 44);
    // 设置 borderStyle
    textField.borderStyle = UITextBorderStyleRoundedRect;


    /***** UIDatePicker */
    UIDatePicker *datePicker = [[UIDatePicker alloc] init];
    self.datePicker = datePicker;
    // 以当地时间方式显示
    datePicker.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"zh"];// 中国的本地化标识是: zh
    // 设置显示的时间格式
    datePicker.datePickerMode = UIDatePickerModeDate;// 年月日
//    datePicker.datePickerMode = UIDatePickerModeDateAndTime;年月日时分秒


    // 设置 textField 的键盘模式
    textField.inputView = datePicker;


    /***** Toolbar */
    UIToolbar *toolbar = [[UIToolbar alloc] init];
    // 设置 toolbar 的 frame
    CGFloat toolbarH = 44;
    CGFloat toolbarW = datePicker.bounds.size.width;
    CGFloat toolbarY = CGRectGetMinY(datePicker.frame) - toolbarH;
    toolbar.frame = CGRectMake(0, toolbarY, toolbarW, toolbarH);
    // 设置 toolbar 的背景色
    toolbar.backgroundColor = [UIColor grayColor];


    /***** 设置 textField 的 toolbar */
    textField.inputAccessoryView = toolbar;


    /***** 往 toolbar 中添加子控件 */
    UIBarButtonItem *nextItem = [[UIBarButtonItem alloc] initWithTitle:@"下一个" style:UIBarButtonItemStylePlain target:self action:@selector(nextBtnDidClicked)];

    UIBarButtonItem *preItem = [[UIBarButtonItem alloc] initWithTitle:@"上一个" style:UIBarButtonItemStylePlain target: nil action:nil];

    UIBarButtonItem *doneItem = [[UIBarButtonItem alloc] initWithTitle:@"Done" style:UIBarButtonItemStylePlain target:self action:@selector(doneDidClicked)];

    UIBarButtonItem *fixedItem  = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFixedSpace target:nil action:nil];

    UIBarButtonItem *flexibleItem  = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFlexibleSpace target:nil action:nil];

    toolbar.items = @[preItem, fixedItem, nextItem, flexibleItem, doneItem];
}

- (void) nextBtnDidClicked{
    // 获取 datePicker 的时间
    NSDate *pickerDate = self.datePicker.date;

    //NSCalendar
    NSCalendar *calendar = [NSCalendar currentCalendar];
    // 截取年,月,日
    NSCalendarUnit unit = NSCalendarUnitYear | NSCalendarUnitMonth | NSCalendarUnitDay;
    NSDateComponents *components = [calendar components:unit fromDate:pickerDate];
    NSInteger year = components.year;
    NSInteger month = components.month;
    NSInteger day = components.day;

    // NSDateFormatter
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    dateFormatter.dateFormat = @"yyyy-MM-dd";

    // NSDate
    NSString *dateString = [NSString stringWithFormat:@"%zd-%zd-%zd",year + 1, month, day];
    NSDate *date = [dateFormatter dateFromString: dateString];

    // 设置 datePicker
    self.datePicker.date = date;

    // 设置 textField
    [self setTextFieldText];
}

- (void) doneDidClicked{
    // 设置 textField
    [self setTextFieldText];

    // 隐藏 datePicker
    [self.view endEditing:YES];
}

-(void) setTextFieldText{
    // 获取 datePicker 的时间
    NSDate *date = self.datePicker.date;
    // 把时间显示在 textField 中
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    dateFormatter.dateFormat = @"yyyy-MM-dd";
    //dateFormatter.dateFormat = @"yyyy-MM-dd hh-mm-ss";
    self.textField.text = [dateFormatter stringFromDate:date];
}

```




