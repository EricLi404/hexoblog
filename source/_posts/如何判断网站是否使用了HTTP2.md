---
title: 如何判断网站是否使用了HTTP2
date: 2017-10-20
tags: network
description: 本文记录了判断网站是否使用了HTTP2的方法。
---

使用 chrome 打开网站，打开 console。

输入`window.chrome.loadTimes()` ，执行

 `google.com`采用了 HTTP/2 结果输出为

```
commitLoadTime
:
1508489268.11
connectionInfo
:
"h2"
finishDocumentLoadTime
:
1508489278.826
finishLoadTime
:
1508489278.84
firstPaintAfterLoadTime
:
0
firstPaintTime
:
1508489282.901
navigationType
:
"Other"
npnNegotiatedProtocol
:
"h2"
requestTime
:
1508489265.936
startLoadTime
:
1508489265.936
wasAlternateProtocolAvailable
:
false
wasFetchedViaSpdy
:
true
wasNpnNegotiated
:
true
```

如图
![这里写图片描述](http://img.blog.csdn.net/20171020165049487?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVpZmx5eQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

则表示采用了 HTTP/2 ,.


----------


反之，`baidu.com` 没有采用 HTTP/2，则如图

![这里写图片描述](http://img.blog.csdn.net/20171020165251464?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVpZmx5eQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

