# UIWebView的基本使用
####1.加载网页
```objc
// 场景一: 加载网页
- (void)loadPage{

    NSURL *url = [NSURL URLWithString:@"http://www.baidu.com/"];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    [self.webView loadRequest:request];

    // 设置自适应
    self.webView.scalesPageToFit = YES;
}
```

####2.加载本地数据
```objc
// 加载本地数据
- (void) loadLocalData{

    NSURL *url = [NSURL fileURLWithPath:@"/Users/apple/Desktop/300-2.jpeg"];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    [self.webView loadRequest:request];
}
```

####3.常用代理方法

- 需设置代理
```objc
self.webView.delegate = self;
```
- 遵循`UIWebViewDelegate`协议

- 重写代理方法

```objc
// 即将加载
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType{

    NSLog(@"%s -- 即将加载", __func__);

    return YES;
}

// 开始加载的时候调用
- (void)webViewDidStartLoad:(UIWebView *)webView{

    NSLog(@"%s -- 开始加载的时候调用", __func__);
}

// 加载结束的时候调用
- (void)webViewDidFinishLoad:(UIWebView *)webView{

    NSLog(@"%s -- 加载结束的时候调用", __func__);
}

// 加载失败的时候调用
- (void)webView:(UIWebView *)webView didFailLoadWithError:(NSError *)error{

    NSLog(@"%s -- 加载失败的时候调用", __func__);
}
```

####后退, 前进, 刷新
```objc
// 后退
- (IBAction)backButtonDidClicked:(id)sender {

    [self.webView goBack];
}

// 前进
- (IBAction)forwardButtonDidClicked:(id)sender {

    [self.webView goForward];
}

// 刷新
- (IBAction)refreshButtonDidClicked:(id)sender {

    [self.webView reload];
}
```

#### 监听前进, 后退的状态
- 是否可以后退
    - `self.webView.canGoBack`
- 是否可以前进
    - `self.webView.canGoForward`

```objc
// 加载结束的时候调用
- (void)webViewDidFinishLoad:(UIWebView *)webView{

    NSLog(@"%s -- 加载结束的时候调用", __func__);

    // 设置是否可以前进和后退
    self.backButton.enabled = self.webView.canGoBack;
    self.forwardButton.enabled = self.webView.canGoForward;
}
```

####设置自适应, 初始滚动位置
```objc
self.webView.scrollView.contentInset = UIEdgeInsetsMake(50, 0, 0, 0);

// 设置自适应
self.webView.scalesPageToFit = YES;
```

####拦截某些链接
需要重写:
`- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType`
这个方法

```objc
// 即将加载
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType{

    NSLog(@"%s -- 即将加载", __func__);

    // 拦截某些链接
    // 如果连接中包含 xxx 就不加载
    NSString *urlStr = request.URL.absoluteString;
    if ([urlStr containsString:@"xxx"]) {
        return NO;
    }

    return YES;
}
```
