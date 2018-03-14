---
layout: post
title:  "适配iOS11和iPhone X总结"
date:   2017-08-11 下午7:29
categories: jekyll update
---




## 1. 首先介绍几个概念

#### WKScriptMessageHandler
WebKit库中有个代理WKScriptMessageHandler就是专门来做交互的，它能让网页通过JS把消息发送给OC，其中协议方法如下   

```
@protocol WKScriptMessageHandler <NSObject>

@required

/*! @abstract Invoked when a script message is received from a webpage.
 @param userContentController The user content controller invoking the
 delegate method.
 @param message The script message received.
 */
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message;

@end

```
从协议中可以看出使用了两个类WKUserContentController和WKScriptMessage。WKUserContentController可以理解为调度器，WKScriptMessage则是携带的数据。

#### WKUserContentController 内容交互控制器
我们要通过webview与JS进行交互，就需要用到这个类，它是用于让JS注入对象的，注入对象后，JS端就可以使用这个方法。它的所有属性及方法说明如下：

```
// 只读属性，所有添加的WKUserScript都在这里可以获取到
@property (nonatomic, readonly, copy) NSArray<WKUserScript *> *userScripts;
 
// 注入JS，即向网页中注入我们的JS方法，这是一个非常强大的功能，开发中要慎用。
- (void)addUserScript:(WKUserScript *)userScript;
 
// 移除所有注入的JS
- (void)removeAllUserScripts;
 
// 添加scriptMessageHandler到所有的frames中，则都可以通过
// window.webkit.messageHandlers.<name>.postMessage(<messageBody>)
// 发送消息
// 比如，JS要调用我们原生的方法，就可以通过这种方式了
// 添加JS调用OC的桥梁，这里的name对应 WKScriptMessage中的name。
- (void)addScriptMessageHandler:(id <WKScriptMessageHandler>)scriptMessageHandler name:(NSString *)name;
 
// 根据name移除所注入的scriptMessageHandler
- (void)removeScriptMessageHandlerForName:(NSString *)name;
```
#### WKScriptMessage
WKScriptMessage 就是 JS 通知OC的数据，其中有两个核心，name 和 body。

```
@interface WKScriptMessage : NSObject

/*! @abstract The body of the message.
 @discussion Allowed types are NSNumber, NSString, NSDate, NSArray,
 NSDictionary, and NSNull.
 */
@property (nonatomic, readonly, copy) id body;

/*! @abstract The web view sending the message. */
@property (nullable, nonatomic, readonly, weak) WKWebView *webView;

/*! @abstract The frame sending the message. */
@property (nonatomic, readonly, copy) WKFrameInfo *frameInfo;

/*! @abstract The name of the message handler to which the message is sent.
 */
@property (nonatomic, readonly, copy) NSString *name;

@end

```

#### WKUserScript

在WKUserContentController中，使用到WKUserScript。WKUserContentController是用于与JS交互的类，而所注入的JS是WKUserScript对象，它的所有属性和方法如下：

```
// JS源代码
@property (nonatomic, readonly, copy) NSString *source;
 
// JS注入时间
@property (nonatomic, readonly) WKUserScriptInjectionTime injectionTime;
 
// 只读属性，表示JS是否应该注入到所有的frames中还是只有main frame.
@property (nonatomic, readonly, getter=isForMainFrameOnly) BOOL forMainFrameOnly;
 
// 初始化方法，用于创建WKUserScript对象
// source：JS源代码
// injectionTime：JS注入的时间
// forMainFrameOnly：是否只注入main frame
- (instancetype)initWithSource:(NSString *)source injectionTime:(WKUserScriptInjectionTime)injectionTime forMainFrameOnly:(BOOL)forMainFrameOnly;
```

## 2. JS 调用 OC

iOS端配置代码如下：

```
config.userContentController = [[WKUserContentController alloc] init];
 
// 注入JS对象名称senderModel，当JS通过senderModel来调用时，我们可以在WKScriptMessageHandler代理中接收到
[config.userContentController addScriptMessageHandler:self name:@"senderModel"];

#pragma mark - WKScriptMessageHandler
- (void)userContentController:(WKUserContentController *)userContentController
      didReceiveScriptMessage:(WKScriptMessage *)message {
  if ([message.name isEqualToString:@"senderModel"]) {
    // 打印所传过来的参数，只支持NSNumber, NSString, NSDate, NSArray,
    // NSDictionary, and NSNull类型
    //do something
    NSLog(@"%@", message.body);
  }
}

```
这里面senderModel就是我们要注入的名称，注入之后，就可以在JS端调用了，传数据统一通过body来传递，类型可以随，但是只支持OC的一些类型（NSNumber, NSString, NSDate, NSArray, NSDictionary, and NSNull类型）

JS端代码如下：

```
    <script type="text/javascript">
      function callJsAlert() {
        alert('Please show alert');    
        window.webkit.messageHandlers.senderModel.postMessage({body: 'Alert'});
      }
    </script>
```

## 3. OC 调用 JS

OC调用JS特别简单，只需要 WKWebView 调用下面方法即可。

```
- (void)evaluateJavaScript:(NSString *)javaScriptString completionHandler:(void (^ __nullable)(__nullable id, NSError * __nullable error))completionHandler;
```

如下
```
- (void)buttonTap
{
    NSString *str = @"OC 调用 JS";
    [self.webView evaluateJavaScript:[NSString stringWithFormat:@"onCallJS('%@')", str] completionHandler:^(id _Nullable data, NSError * _Nullable error) {
        if (error) {
            NSLog(@"错误：%@", error.localizedDescription);
        }
    }];
}
```
OC将通知发送给JS后，JS要响应这个onCallJS方法。在html文件中添加 onCallJS方法

```
 function onCallJS(params) {
     document.getElementById('jsParamFuncSpan').innerHTML = params;
}
```

## 4. demo链接 [WKWebViewDemo](https://github.com/guohongwei719/WKWebViewDemo.git)


































