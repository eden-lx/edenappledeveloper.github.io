---
title: iOS中webview和wkwebview获取网页title的方式
date: 2019-03-29 10:17:19
tags:
---


UIWebView获取title的方式

    在代理webViewDidFinishLoad方法中
    -(void) webViewDidFinishLoad:(UIWebView *)webView {  
    [UIApplicationsharedApplication].networkActivityIndicatorVisible =NO;  
    self.title ＝  [webView stringByEvaluatingJavaScriptFromString:@"document.title"];//获取当前页面的title  
    }

<!-- more -->

WKWebView获取title的方式比较复杂点
1.wkWebView初始化完毕笔后设置代理并添加观察者

    例如：
    _wkWebView.UIDelegate = self;
    [_wkWebView addObserver:self forKeyPath:@"title" options:NSKeyValueObservingOptionNew context:NULL];


2.实现代理

    //WkWebView的 回调
    -(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context{
        if ([keyPath isEqualToString:@"title"]) {
            if (object == self.wkWebView) {
                self.title = self.wkWebView.title;
            } else {
                [super observeValueForKeyPath:keyPath ofObject:object change:change context:context];
            }
        } else {
            [super observeValueForKeyPath:keyPath ofObject:object change:change context:context];

        }

    }


3.移除代理

    - (void)dealloc{
        [_wkWebView removeObserver:self forKeyPath:@"title"];
    }
