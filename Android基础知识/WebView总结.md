﻿WebView总结
===

换了家公司，需要做浏览器相关的功能，一直没有用到过`WebView`，相对比较陌生，简单的学习了一下算是完成了功能，今天能有点时间，简单的总结一下：              
在`Android`中有`WebView Widget`，它内置了`WebKit`引擎，同时，`WebKit`也是`Mac OS X`的`Safari`网页浏览器的基础。`WebKit`是一个开源的浏览器引擎，`Chrome`浏览器也是基于它的。所以很多表现`WebView`和`Chrome`是一样的。          

很多文章中多会说在使用`WebView`之前，要在`AndroidManifest.xml`中添加 如下权限：           
`<uses-permission android:name="android.permission.INTERNET"></uses-permission>`否则会出`Web page not available`错误。其实这是不全面的，如果我加载本地的页面是不用该权限的。

- 设置WevView要显示的网页方法有很多：
    - `mWebView.loadUrl(“http://www.google.com“);` // 网络
    - `mWebView.loadUrl(“file:///android_asset/XX.html“);` // 本地页面,这里的格式是固定的，文件要放到`assets`目录下
	- `mWebview.postUrl(String url, byte[] postData); // 加载页面使用`Post`方式，`postData`为参数`

	    ```java
		String postData = "password=password&username=username";
		mWebview.postUrl(url, EncodingUtils.getBytes(postData, "base64"));
		```
    - mWebView.loadData(htmlString, "text/html", "utf-8"); // 加载Html数据
	    ```java
		String htmlString = "<h1>Title</h1><p>This is HTML text<br /><i>Formatted in italics</i><br />Anothor Line</p>";
		mWebView.loadData(htmlString, "text/html", "utf-8"); // 加载Html数据
		```
		- `loadData()`不能加载图片内容，如果要加载图片内容或者获得更强大的Web支持请使用`loadDataWithBaseURL()`。
		- 显示乱码
		    `WebView`一般为了节省资源使用`UTF-8`编码，而`String`类型的数据主要是`Unicode`编码，因此在`loadData()`的时候需要设置相应编码让其将`Unicode`编码转成`UTF-8`但是有些时候设置后还是会出现乱码，这是因为还需要为`WebView`中的
			`Text`设置编码，

			```java
			WebView mWebView = (WebView)findViewById(R.id.webview) ;
			String content = getUnicodeContent() ;
			mWebView.getSettings().setDefaultTextEncodingName(“UTF -8”) ;
			mWebView.loadData(content, “text/html”, “UTF-8”) ;
			```

- 设置WebView基本信息：
	 - 如果访问的页面中有`Javascript`，则`webview`必须设置支持`Javascript`。
	    `webview.getSettings().setJavaScriptEnabled(true);  `
	- 触摸焦点起作用
	    `requestFocus() // 如果不设置的话，会出现不能弹出软键盘等问题。`
	- 取消滚动条
	    `this.setScrollBarStyle(SCROLLBARS_OUTSIDE_OVERLAY);`
		
- Back键的处理     
    如果用`webview`点链接看了很多页以后，如果不做任何处理，点击系统`Back`键，整个浏览器会调用`finish()`而结束自身，如果希望浏览的网页回退而不是退出浏览器，需要在当前`Activity`中处理并消费掉该`Back`事件。

	```java
	 public boolean onKeyDown(int keyCoder,KeyEvent event){
		if(webView.canGoBack() && keyCoder == KeyEvent.KEYCODE_BACK){
			webview.goBack();   //goBack()表示返回webView的上一页面
			return true;
		}
		return false;
	 }
	```
	
- WebView中Padding没有效果	       
    `WebView`中使用`Padding`没有效果，我们在`WebView`外层包上一层布局就会有所改进，但是不能完全解决问题，正确的做法是在`WebView`的加载`css`中增加`Padding`
	
- WebViewClient
    如果希望点击链接由自己处理，而不是新开`Android`的系统`browser`中响应该链接。给`WebView`添加一个事件监听对象`WebViewClient`并重写其中的一些方法： `shouldOverrideUrlLoading`对网页中超链接按钮的响应。当按下某个连接时`WebViewClient`会调用这个方法，并传递按下的url。
	1. 接收到 Http 请求的事件 
	
	    `onReceivedHttpAuthRequest(WebView view, HttpAuthHandler handler, String host, String realm) `            
	2. 打开链接前的事件
	
	    `public boolean shouldOverrideUrlLoading(WebView view, String url) { view.loadUrl(url); return true; } `           
	    这个函数我们可以做很多操作，比如我们读取到某些特殊的URL，于是就可以不打开地址，取消这个操作，进行预先定义的其他操作，这对一个程序是非常必要的。
	3. 载入页面完成的事件
	
	    `public void onPageFinished(WebView view, String url){ } `              
	    页面载入完成，于是我们可以关闭`loading`条，切换程序动作。
	4. 载入页面开始的事件
	
	    `public void onPageStarted(WebView view, String url, Bitmap favicon)`              
	    这个事件就是开始载入页面调用的，通常我们可以在这设定一个loading的页面，告诉用户程序在等待网络响应。
 

---

- 邮箱 ：charon.chui@gmail.com  
- Good Luck! 
