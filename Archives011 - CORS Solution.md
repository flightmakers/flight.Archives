## Title/ 浏览器跨域(CrossOrigin)请求的原理, 以及解决方案详细指南 #flight.Archives011
> 序:  
> 最近看到又有一波新的创作活动了, 官方给出的话题中有一个"为什么XHR不能跨域请求资源", 看起来像一道很简单的面试题哈哈哈  
> 作为前端爱好者, 不妨借此机会研究一下跨域, 总结一篇文章出来. 于是我就安排了flight.A011的FocusList#写作计划.   
>
> 简介:  
> 一篇最简洁高效的跨域请求指南 made with ❤ by `忘我思考`
> 
> 注:  
> 1. 本文中XHR表示 `XMLHttpRequest` 对象.
> 2. CORS表示`Cross-origin resource sharing`, 译为跨域资源共享.
> 3. 请求头即 Request Headers, 响应头即 Response Headers.

### Tag/ 浏览器跨域(CORS)限制介绍: 为什么要限制XHR跨域请求, 如何触发跨域限制
> 大佬们可以直接看下一章解决方案, 这里会解释得详细一点方便新手入门(谁都曾是萌新嘛), 不了解CORS的来这补补课吧~  

跨域, 就是站A的请求要访问站B的服务器. 
如果 `协议`(protocol), `域名`(domain), `端口`(port) 三者有任意一处不同, 浏览器就认为这是两个不同的网站, 会触发跨域安全限制.  
```
详细的说, 就是:
[https://][example.com][:80]/path?query#heading-1
只有打方括号内的内容完全一致才是"同源", 也就是说同源请求URL中只有端口后的内容是可以不一致的, 否则就是跨域
请注意: https协议的默认端口是443, http协议默认端口是80. 默认端口可以省略不写.
也就是说 https://example.com:443/... 和 https://example.com/... 是同源, http同理.
```

#### ->> 为什么浏览器要有跨域限制
设想这样的场景:  
我是不知名小Blog主, 你现在在访问我的博客网站, 然后我突发奇想, 想要用自动JS程序发送请求到 weibo 的服务器, 让你自动关注我的微博.  
如果这能实现, 那浏览器就太不安全了, 网站主可以在后台做很多危险请求(如获取你在 weibo 的登录信息等).  
所以浏览器都有一个跨域限制, 阻止不安全的第三方内容请求.

举个例子:

![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823125541942-708608213.png)

如果我在Segmentfault官网的Console访问我的博客园首页, 就会触发XHR跨域限制, 禁止读取返回内容.   

而另一个情景:  
你是一个小站长, 在网站使用Vue.js, 于是添加了一个CDN脚本(如 `unpkg` 等)  
结果浏览器错误的因为跨域限制自动拦截请求导致你无法使用CDN脚本.  
这显然是不合理的, 所以现在的浏览器都会智能根据对方(网站B)的 `响应头` 中的 `Access-Control-Allow-Origin`属性, 来判断你是否可以读取对方返回的内容.  
比如 `unpkg` 就把 `Access-Control-Allow-Origin` 属性设为了 `*`, 方便外域调用CDN.  

![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823123244212-1866946288.png)

像React也建议你验证CDN的请求头属性, 避免使用出现跨域限制问题.  

也就是说, 你能不能访问第三方资源, 其实是由对方(响应头)决定的(这其实也是合理的, 按道理请求别人的东西就要经过允许呀).  
对方服务器可以通过你的 `cookie` `referer` 等 `请求头` 参数来判断请求的安全性, 决定是否返回对应内容.  
注意: 纯前端是不能纂改 `referer` 请求头的, 否则会报错.  

#### ->> 浏览器对请求的详细划分方法: 简单请求和复杂请求
不过浏览器的跨域限定其实有详细的划分, 这里有必要提到一下:  
请求分为 `简单请求` 和 `复杂请求` 两种.  
对于 `简单请求`, 要满足以下所有条件("与"关系).
1. 请求方法是 `GET`, `POST`, `HEAD` 之一.
2. 人为设置的请求头不超过以下属性: `Accept`, `Accept-Language`, `Content-Language`.
3. `Content-Type` 属性值是 `text/plain`, `multipart/form-data`, `application/x-www-form-urlencoded` 之一.
4. 请求中的任意 `XMLHttpRequestUpload` 对象均没有注册任何事件监听器; `XMLHttpRequestUpload` 对象可以使用 `XMLHttpRequest.upload` 属性访问.
5. 请求中没有使用 `ReadableStream` 对象.

其中前3点比较常用. 如果请求不满足以上5个条件的任意之一, 则是复杂请求.

浏览器区分 `简单请求` 和 `复杂请求` 是为了兼容表单(form), 因为历史上表单一直可以发出跨域请求.

#### ->> XHR请求在浏览器里是如何被发出去的? 浏览器对简单请求和复杂请求的不同处理方式
> 这里我觉得阮一峰的文章已经写得很好了(在文末有链接), 所以, 如有雷同(不对, 就是雷同)... 不要觉得我是在抄袭, 只是按照他的`创意共享3.0许可证`合法借鉴哈.
1. 浏览器对简单请求的处理方式!  
   如果一个XHR满足简单请求的所有判定, 那它会被这样发出去:
   ```
   // 直接发送包含Origin请求头的XHR请求到对方服务器
   浏览器会自动在头信息之中, 添加一个Origin字段.
   如: Origin: http://api.gold.xitu.io (现在掘金被字节收取就变味了, 好怀念Ming在的时间啊...)
   Origin是"起源"的意思, 包含协议 + 域名 + 端口, 会告诉对方请求是从哪个源发出的.
   如果Origin指定的源不在许可范围内(比如在cnblogs中添加访问Bilibili的代码), 对方会返回一个不包含Access-Control-Allow-Origin的响应头, 
   浏览器就知道出错了, 从而抛出一个错误, 被XMLHttpRequest的onerror回调函数捕获. 
   注意: 这种错误无法通过状态码识别, 因为HTTP回应的状态码有可能是200.
   
   如果Origin指定的域名在许可范围内, 服务器返回的响应, 会多出几个头信息字段:
   Access-Control-Allow-Origin: http://api.bbb.com
   Access-Control-Allow-Credentials: true
   Access-Control-Expose-Headers: FooBar
   上面这些以 Access-Control- 开头的字段, 都表示对方服务器对请求的访问限制.
   
   下面介绍一下这三个属性: 
   1. Access-Control-Allow-Origin(必须属性): 可以是URL, 表示对请求时Origin字段的值的限制, 也可以是 *, 表示接受任意域名的请求, 像CDN代码存储服务之类的.
   2. Access-Control-Allow-Credentials(可选): 一个布尔值, 只能为true, 表示是否允许发送Cookie. 默认情况下(不填该属性), 则Cookie不包括在跨域请求之中.
   3. Access-Control-Expose-Headers(可选): 跨域请求时, XHR对象的getResponseHeader()方法只能拿到6个基本字段: Cache-Control, Content-Language, Content-Type, Expires, Last-Modified, Pragma. 如果想拿到其他字段, 必须在Access-Control-Expose-Headers中指定. 上面的例子指定getResponseHeader('FooBar')可以返回FooBar字段的值.
   
   ->> Details: withCredentials 属性
   上面说到, 跨域请求默认不发送Cookie和HTTP认证信息. 如果要把Cookie发到服务器, 一方面要服务器同意, 指定Access-Control-Allow-Credentials字段为true.
   另一方面, 网站主必须在AJAX请求中打开withCredentials属性. 像这样:
   
   var xhr = new XMLHttpRequest();
   xhr.withCredentials = true;

   否则, 即使服务器同意发送Cookie, 浏览器也不会发送. 这种情况下服务器即使要求设置Cookie, 浏览器也不会处理. 

   但是, 如果省略withCredentials设置, 有的浏览器还是会一起发送Cookie. 这时, 可以显式关闭withCredentials:
   xhr.withCredentials = false;
   
   需要注意的是, 如果要发送Cookie, Access-Control-Allow-Origin就不能设为星号, 必须指定明确的, 与请求网页一致的域名. 同时, Cookie依然遵循同源政策, 只有用服务器域名设置的Cookie才会上传, 其他域名的Cookie并不会上传, 且(跨域的)原网页代码中的document.cookie也无法读取对方服务器域名下的Cookie.
   ```
   
2. 浏览器对复杂请求的处理方式!
   ```
   // 先发送预检请求(preflight), 请求通过后, 发送XHR到对方服务器
   浏览器会先询问服务器, 当前网页所在的域名是否在服务器的许可名单之中, 以及可以使用哪些HTTP动词和头信息字段. 只有得到肯定答复, 浏览器才会发出正式的XMLHttpRequest请求, 否则就报错.
   
   来了, 这是一段JS代码!
   var url = 'http://api.aaa.com/cors';
   var xhr = new XMLHttpRequest();
   xhr.open('PUT', url, true);
   xhr.setRequestHeader('X-Custom-Header', 'value');
   xhr.send();
   
   可以看到使用了 PUT 方法, 还人为设置了X-Custom-Header请求头, 所以这是个复杂请求qwq...
   
   所以浏览器会发送一个预检请求, 预检请求的请求头像这样:
   OPTIONS /cors HTTP/1.1
   Origin: http://api.bbb.com
   Access-Control-Request-Method: PUT
   Access-Control-Request-Headers: X-Custom-Header
   Host: api.aaa.com
   ...(以及一些更多的无关请求头)
   
   预检请求使用了 OPTIONS 方法, 表示这个请求是用来询问的. 请求头中, 关键字段是Origin, 表示请求来自哪个源.
   而 Access-Control-Request-Method 和 Access-Control-Request-Headers 你一看就明白是什么了, 分别是请求方法和人为设置了哪些属性.
   
   服务器受到预检请求后, 判断请求头是否OK, 如果OK就可以做出回应啦!
   响应头中关键信息是这几个:
   Access-Control-Allow-Origin: http://api.bbb.com
   Access-Control-Allow-Methods: GET, POST, PUT
   Access-Control-Allow-Headers: X-Custom-Header
   
   解释一下~
   1. Access-Control-Allow-Origin: 表示允许的请求源, 也可以是*表示随便什么网站.
   2. Access-Control-Allow-Methods: 就是允许的方法啊!
   3. Access-Control-Allow-Headers: 允许设置的请求头参数.
   
   好了, 那如果服务器不同意预检请求怎么办呢~
   
   会返回一个正常的HTTP回应, 但是没有任何CORS相关的头信息字段. 这时，浏览器就认为, 服务器不同意预检请求.
   因此触发一个错误, 被XMLHttpRequest对象的onerror回调函数捕获. 
   控制台会打印出如下的报错信息:
   
   XMLHttpRequest cannot load http://api.aaa.com.
   Origin http://api.bbb.com is not allowed by Access-Control-Allow-Origin.
   
   服务器回应的其他CORS相关字段如下:
   Access-Control-Allow-Methods: GET, POST, PUT //所有支持的方法
   Access-Control-Allow-Headers: X-Custom-Header //如果浏览器请求包括Access-Control-Request-Headers字段, 则Access-Control-Allow-Headers字段是必需的, 表示支持的所有请求头字段.
   Access-Control-Allow-Credentials(可选): true //也是表示Cookie
   Access-Control-Max-Age(可选): 1728000 //表示本次预检请求的有效期, 单位为秒. 比如这个就设定了本次请求有效期是20天(1728000秒), 期间不再次发送请求.
   ```

#### ->> 案例: 解决一个博客音乐播放器的跨域问题
浏览器的安全限制其实也不是完美无缺的, 前端的话如果不涉及验证码, 登录, 加密之类的复杂操作, 通过一些技术手段想要突破跨域限制, 也不是没有可能.  
无后端支持也是可以实现跨域的, 不过一般要在用户知情的情况下配合操作.  
其实就是你可以实现一些跨域请求, 但是是否能够获取请求内容, 本来是由对方服务器请求头决定的, 现在决定权在于用户.  
比如你想要在你的博客中添加一个音乐播放器, 播放QQ音乐的内容.  
而众所周知, 腾讯那边的盗链(可以理解为跨域)限制是很强的!  
所以一般我的实现是配合后端, 也就是访问QQ音乐网址, 把音乐通过浏览器控制台Network栏获取请求资源URL下载保存到自己的服务器.  
然后直接访问自己的服务器获取音乐资源(QQ音乐一般是m4a, 网易一般是mp3, 不过顺便一提网易取消了盗链保护, 所以一般可以直接用网易服务器, 只是有的音乐有版权保护就不能外域播放了).  
而如果要纯前端实现跨域音乐播放则可以使用 `<iframe>`, Chrome参数纂改, Chrome插件等方法来实现盗链.  

这些《神奇》的方法也就是下一章会详细介绍的内容啦~ `[奸笑]`

### Tag/ 重头戏开始了! 突破跨域限制的9种解决方案
1. 使用 `iframe` 实现  
   `iframe` 标签可以将跨域内容嵌入你的网页, 比如在博客中添加一个Bilibili视频 或者 Codepen Demo的 `iframe`.  
   不过 `iframe` 有两个缺点: 
      1. 很多服务器会直接拒绝跨域 `iframe` 请求,
      2. 跨域访问 `iframe` 中的内容会遭到限制qwq.  
   放几张图体会一下:  
   
   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823141259237-1770222604.png)

   如果在阮一峰的Blog中插入 iframe(cnblogs博文), 会被拒绝qwq...

   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823141543741-1985702326.png)

   而如果在站内插入iframe则没有问题, 看来博客园为了安全考虑目前关闭了跨域请求.

   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823141959141-1658069826.png)

   而Bilibili就比较开放了, 目前可以插入iframe.

   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823142253933-623219261.png)

   不过如果你想通过iframe读取或者篡改Bilibili网页的内容也是不可以的啦~

   所以这终究只是一个向用户"展示内容"的方法而已, 限制很多, 如果要读取/篡改内容, 还是得同源才行~
   
2. 使用CORS方法(跨域资源共享)  
   这个方法也就是第一章中介绍的, 很详细了. 不用再重复一遍啦.
   
   但是这种方法也是受到对方服务器的种种限制, 不能为所欲为qwq...

3. 搞一个浏览器插件配合(终极大招, 直接无视跨域限制)  
   嘿嘿嘿! 没想到吧! 竟然还有这种方法?!  
   网上这方面的文章一般都不会想到有这种神奇也是非常强大的方法, 不过这也是非常实用的.  
   如果要我来, 很可能会采用这种方法, 可以不用后端支持进行跨域!  
   如果你的网站面向的对象主要是爱好破解的极客/有探索精神的玩家, 可以向他们推荐你的Chrome插件, 或者是一段油猴脚本.  
   比如让用户简单的copy&paste添加一段你的油猴(TemperMonkey)脚本, 就可以实现跨域请求啦!  
   (现在油猴/Chrome插件在极客/开发者手里也已经很普及了吧~, 想想你装了多少插件...  
   (当然一个都没装的勿喷!
   
   不过现在Chrome插件那么多, 可是你... 能不能独立开发一个扩展插件呢? 恐怕很难吧...  
   (其实如果你懂前端, 插件开发也不难, 只要了解Chrome插件专门的一些方法就行了.  
   那么这里就简单介绍一下实现方法, 如果你想开发一个完整实用的插件, 网上也有不少文档了. (Chrome Developer中也有详细的介绍

   不如先学习一下别人是怎么玩的. 我们随便打开一个别人开发的Chrome插件的文件夹(我打开的是Tempermonkey), 是这幅景象:
   
   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823155350617-1645768006.png)

   其中有一个 `manifest.json` 文件, 和你的插件支不支持跨域密切相关, 比如油猴的长这样:
   ```json
   {
   "background": {
   "page": "background.html"
   },
   "browser_action": {
   "default_icon": {
   "16": "images/icon_grey16.png",
   "19": "images/icon_grey19.png",
   "24": "images/icon_grey24.png",
   "32": "images/icon_grey32.png",
   "38": "images/icon_grey38.png"
   },
   "default_popup": "action.html",
   "default_title": "Tampermonkey"
   },
   "commands": {
   "open-dashboard": {
   "description": "Open dashboard"
   },
   "open-dashboard-with-running-scripts": {
   "description": "Open dashboard with the current tab's URL used as filter"
   },
   "open-new-script": {
   "description": "Open new script tab"
   },
   "toggle-enable": {
   "description": "Toggle enable state"
   }
   },
   "content_scripts": [ {
   "all_frames": true,
   "js": [ "rea/common.js", "content.js" ],
   "matches": [ "file:///*", "http://*/*", "https://*/*" ],
   "run_at": "document_start"
   } ],
   "content_security_policy": "script-src 'self'; object-src 'self';",
   "default_locale": "en",
   "description": "The world's most popular userscript manager",
   "differential_fingerprint": "1.2ae827c5b75f9e167b3ed0bf65d0c330028dd0acf93a84a8f0f2d7e096024ba3",
   "icons": {
   "128": "images/icon128.png",
   "32": "images/icon.png",
   "48": "images/icon48.png"
   },
   "incognito": "split",
   "key": "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCwuYtg7kY2YyNieOkV9pK/qcXwUXu0CFUO0zU6DLAGAJZK7zxrHlwg9a+zFH7CXqgH7zSfRce9KiYOHJaLPBXM66uPCliiQ6Q+bFaNZx1FxLXkZTFnlyPh8kkwwohLJeSQ9NQXqEfeTepDj5BRufAR48az0MC5aUTEj+fFXbzX7QIDAQAB",
   "manifest_version": 2,
   "minimum_chrome_version": "64.0.0.0",
   "name": "Tampermonkey BETA",
   "offline_enabled": true,
   "optional_permissions": [ "downloads" ],
   "options_page": "options.html",
   "options_ui": {
   "chrome_style": false,
   "open_in_tab": true,
   "page": "options.html"
   },
   "permissions": [ "notifications", "unlimitedStorage", "tabs", "idle", "webNavigation", "webRequest", "webRequestBlocking", "storage", "contextMenus", "chrome://favicon/", "clipboardWrite", "cookies", "declarativeContent", "\u003Call_urls>" ],
   "short_name": "TM BETA",
   "update_url": "https://clients2.google.com/service/update2/crx",
   "version": "4.14.6142"
   }
   ```
   这个是Chrome插件的灵魂, 设置了各种属性, 包括Chrome插件的图标, 点击Chrome插件图标打开的popup页面等...  
   这里和跨域相关的只有一个: 就是 `permissions` 属性.
   
   这个属性设置了插件有哪些权限, 比如可以随意访问哪些网站.

   比如油猴的, 就有一个 `"\u003Call_urls>"` 在 `permissions` 属性里.  
   其中 `\u003C` 其实就是 `<` 的Unicode编码, = `<all_urls>`, 也就是这个插件定义的JS代码可以随意访问各个网站
   
   而在用户眼中就是这幅景象:

   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823163035520-1304739504.png)

   关于Chrome插件配合网站实现跨域的文档, 可以参考这篇 https://wizardforcel.gitbooks.io/chrome-doc/content/23.html, 总结得很完整了.

   而我搜索的时候, 惊喜的发现, 还有人开发了这样一个插件(不是我开发的啊):  
   名字叫 "Allow CORS", 挺好用的, 初衷是帮助开发者跨域调试网站.  
   
   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823161719304-579152420.png)
   
   用户评价也挺不错的.  
   网址就是 https://chrome.google.com/webstore/detail/allow-cors-access-control/lhobafahddgcelffkeicbaginigeejlf  
   用途当然就是和名字一样, 可以解除跨域限制. 所以你只要让用户轻轻一点去装上这个插件, 就可以无视跨域限制啦!  
   不过Chrome因为Google原因, 在国内有限制, 面对一般的用户Chrome也支持上传本地包来添加插件, 也很简单, `下载->一点打开开发者模式->一拖添加插件` 就OK啦!
   
   好了这个方法是真的tql.

4. 配合客户端本地代理VPN(终极大招)  
   看完第三个方法, 我觉得其他的都要退下了, 毕竟第3个方法应该是最简单的了.  
   第四个原理差不多, 不过开发起来是真的麻烦啊.  
   要让用户装一个系统应用, 本地VPN, 就像Charles, Fiddler这样.  
   然后在本地自动修改 Referer 和 Origin 等请求头, 假装一个正常的用户请求.  
   总之不是一个优秀的解决方案吧.

5. 配合后端(终极大招)  
   本文着重纯前端开发, 我也不太懂后端(基本的php除外, 用来建博客要用到)  
   这个方法也是很实用的, 但是不详细介绍了.  
   就是针对不涉及用户在浏览器中cookie的情况, 通过后端语言(如Java)正常请求来获取资源.  
   再返回到前端, 请求一个本站资源(其实转自对方站).  
   
   比如第一章中提到的音乐播放器, Github上就有人用配合后端实现了! 还有1.9k个star!   
   不过这个解析下载音乐因为侵权了, 所以2018年起暂停维护了, 不过下载下来依然可以使用.  

   不过只能下载免费音乐, 付费听的音乐会ErrorHappensQwQ.  
   因为只是后端搜索解析一下, 音乐资源依然是来自对方服务器(所以QQ音乐盗链保护还是生效, 不能听啊... 网易就没事, 毕竟网易一向开放)  
   想了解一下的, 可以啪的点进去: https://github.com/maicong/music

6. JSONP方法  
   对于 `script` 标签的资源, 浏览器是没有跨域限制的! (对方按照referer/origin属性不返回数据的情况除外)
   
   比如网上就有这样的实现:
   ```html
   <!-- 原生JS实现 -->
   <script type="text/javascript">
     window.jsonpCallback = function(res) {
       console.log(res);
     };
   </script>
   <script
     src="http://localhost:80/api/jsonp?&cb=jsonpCallback"
     type="text/javascript"
   ></script>
   <!-- JQuery Ajax实现 -->
   <script src="https://cdn.bootcss.com/jquery/3.5.0/jquery.min.js"></script>
   <script>
     $.ajax({
       url: "http://localhost:80/api/jsonp",
       dataType: "jsonp",
       type: "get",
       data: {
         msg: "hello"
       },
       jsonp: "cb",
       success: function(data) {
         console.log(data);
       }
     });
   </script>
   ```
   嗯, 就是这样用的, 很简单获取Script内容, 不过也只支持Script qwq...  

7. WebSocket实现  
   WebSocket是HTML5的一个特性, 可以让浏览器化被动为主动发送内容, 适合实时直播等场景(比如字节的掘金直播就用到了这种方法)  
   
   ![](https://img2020.cnblogs.com/blog/2451762/202108/2451762-20210823165849207-1254681993.png)
   
   WebSocket其实虽然有用(本来就没有CORS的跨域限制), 但其实也没有办法获取HTTP资源QAQ.
   
   所以这个方法也不推荐吧!

8. window.postMessage() 方法  
   这个方法可以安全地实现跨源通信!   

   不了解这个方法, 可以去这里补补课~ ->> https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage
   
   在《HTML5CSS3权威指南》中也有介绍, 但是在实战中这个方法使用也比较少哈~

   这个方法可以进行不同界面的消息传递:  
   比如多窗口之间, 网页和其iframe之间.
   
   比如QQ音乐就用到了这种方法.  
   如果你开启了多个播放页面, QQ音乐会通过这个方法多界面之间通信, 自动暂停之前的播放页, 避免同时播放多个音乐.
   
   当然正因为它可以 "安全地" 实现跨源通信, 所以这个通信方法也是需要对方服务器认可的. 所以如果要做不正经的事(对方不允许的情况下强行获取资源)也没有用啊...
   
9. 使用"允许跨域"方法打开浏览器  
   既然是浏览器搞的限制, 能不能关闭这个限制呢?  
   是可以的!
   
   就以这种方法打开(Windows, xxx是你的data目录):
   ```
   C:/.../.../path/chrome.exe --disable-web-security --user-data-dir=xxxx
   ```
   Mac也可以! 也是一样的.
   ```
   --disable-web-security --user-data-dir=~/Downloads/chrome-data
   ```
   
   就是这样, 不过要重启Chrome才行, 用户可能不愿意, 所以还是插件这个方法我最推荐.

当然, 如果不正当获取了第三方内容是可能会有律师函警告的~  
所以说, 本文只是对跨域的介绍和技术上的探索与研究, 仅此而已. (你懂的`[doge]`  

### ->> Details
#### 附录 - CORS请求与JSONP的比较
CORS与JSONP的使用目的相同, 但是比JSONP更强大.

JSONP只支持GET请求, CORS支持所有类型的HTTP请求. JSONP的优势在于支持老式浏览器, 以及可以向不支持CORS的网站请求数据.

<!--
### ->> flight.frontendBeautiful
> 待添加

### ->> flight.Player
> 待添加

### ->> flight.Playground
> 待添加

### ->> flight.QuickDemo
> 待添加

### ->> See also
> 待添加
-->

### ->> Reference link
> MDN中文文档 https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS
>
> MDN 英文文档 https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
> 
> 阮一峰 https://www.ruanyifeng.com/blog/2016/04/cors.html
> 
> 秋风的笔记 https://segmentfault.com/a/1190000022398875
> 
> 思否 - 安静de沉淀 https://segmentfault.com/a/1190000011145364
> 
> 掘金 - 小铭子 https://juejin.cn/post/6844903882083024910
> 
> 掘金 - JackySummer https://juejin.cn/post/6861553339994374157
> 
> Chrome Developers https://developer.chrome.com/docs/extensions/mv3/xhr/
> 
> Html5Rocks https://www.html5rocks.com/en/tutorials/cors//
> 
> 博客园 - 小火柴的蓝色理想 https://www.cnblogs.com/xiaohuochai/p/6036475.html
> 
> 知乎 - 单纯前端能否解决跨域问题? https://www.zhihu.com/question/302245173
> 
> MDN - XHR介绍 https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest
> 
> MDN - XHR介绍(英文) https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest
> 
> MDN - Access-Control-Allow-Origin 属性介绍 https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin
> 
> MDN - Access-Control-Allow-Origin 属性介绍(英文) https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin
> 
> StackOverflow - How does Access-Control-Allow-Origin header work? https://stackoverflow.com/questions/10636611/how-does-access-control-allow-origin-header-work
> 
> StackOverflow - CORS with XMLHttpRequest not working https://stackoverflow.com/questions/25296455/cors-with-xmlhttprequest-not-working
> 
> W3 - CORS Enabled https://www.w3.org/wiki/CORS_Enabled

### ->> Version History
> 现在版本为V1.0
> 详见 Github(@flightmakers)
>
> 2021.8.23 发布V1.0
