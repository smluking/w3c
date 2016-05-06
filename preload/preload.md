#Preload

####使用场景
1.需要通过某些注入元素或其他判断条件才能加载的资源
2.需要通过XMLHttpRequest加载的资源
中间可能有一大批的脚本执行，可能导致阻塞，影响页面展示

####使用方式
1.link标签
```sh
<link rel="preload" href="./other.css" as="style">
```
2.js代码
```sh
<script>
    var res = document.createElement("link");
    res.rel = "preload";
    res.as = "style";
    res.href = "./other.css"
</script>
```

#### 功能检测

#### 与prefetch的区别
| Tables     | prefetch    | preload       | subresource |
| ---------- | :-------:   | :------:      | --------:   | 
| when       |可忽略，低优先级| 强制性，高优先级 | 优先级非常低(低至没有效果，chrome) |
| how        |下一页        | 当前页         | 未知         |

as属性：
1.浏览器可以设置正确的资源加载优先级，可以确保资源根据其重要性依次加载，所以，preload既不会影响重要资源的加载，又不会让次要资源影响自身的加载
［as属性被用来初始化请求头和确定优先级，假如as属性被省略，则会被当成XMLHttpRequest来请求］
2.浏览器可以确保请求时符合内容安全策略，比如，Content-Security-Policy:script-src 'self'，只允许浏览器执行自己的服务器脚本，as值为script的外部服务器资源就不会被加载［代证］
3.浏览器能根据as值发送适当的Accept头部信息
4.浏览器通过as值能得知资源类型，因此当获取相同资源时，浏览器能够判断前面获取的资源是否能够重用
onload,onerror：
你可以定义资源加载完毕后的回调函数
(preload和subresource是不支持的)
```sh
<script>
function preloadFinished() {...}
function preloadError() {...}
</script>
<!--- listen FOR load and error events --->
<link rel="preload" href="xx.xx" as="xxxx" onload="preloadFinished()" onerror="preloadError()">
```

####资源加载时机－－when［待验证：两条以上］
1. ☑️header头部包含一个preload link
2. ☑️一个preload link标签被插入到document中时 
3. ☑️已有的一个link标签变成preload link时     
4. ☑️在document中已存在的preload link的href属性改变时  
4. 。。在document中已存在的preload link的crossorigin属性set, changed,removed时
5. ☑️在document中已存在的preload link的as属性set，changed
5. ☑️在document中已存在的preload link的as属性之前因为指定无效类型而导致资源获取失败时，之后的任何set，changed,removed都能促使重新加载
5. ☑️在document中已存在的preload link的type属性之前因为指定无效类型而导致资源获取失败时，之后的任何set，changed,removed都能促使重新加载
6. 在document中已存在的preload link的media属性（媒介类型判定）恰好符合用户使用环境，则应加载，否则不应下载。

注意：在请求的过程中，当代理检测到当前发请求的link元素的href属性changed，removed，或者置空，则中止当前请求

####资源加载流程－－how［待验证：两条以上］
1.如果检测到无href值，终止
2.解析href的URL（必须是绝对路径）
3.上述解析失败，终止
4.验证as属性值是否是合法的请求类型。如果发现as属性省略，则初始化为空值；如果发现as属性值不合法，发起error事件，终止
5.当有corossorigin属性时，尝试CORS－enabled fetch，（本条没理解＝＝！）
［Do a potentially CORS-enabled fetch of the resulting absolute URL, with the mode being the current state of the element's crossorigin content attribute, the origin being the origin of the link element's node document, destination set to request destination, and the default origin behavior set to taint.
］

####与加载资源获取成功后 ［待验证：两条以上］
1.如果下载成功，为该link元素触发onload事件任务；否则，触发onerror事件
2.将请求添加到preload response cache中（TODO）［preload response cache是啥］

a.user agent不允许主动执行或者应用资源，特别是在影响当前页面的时候。［碰到第二条preload请求时才会执行当前响应的资源］－－－［待验证：两条以上］
b.user agent会一直保持上一个preload link的响应信息，直到发现另一个不同资源的preload请求或者该响应已经失效。
c.重复定义的preload link请求不会重复下载或者重复生效，即使响应是no－cache形式。


####早期获取关键资源
～～～～对字体的提前加载～～～～～
当主解析器被大JS文件阻塞时，我们可以用preload link来initiate early resources fetch。
因为preload parser并不执行JS，只是对CSS进行浅解析，也就是说preload fetch 会在相关document 解析器能够处理（有空处理）资源的声明后再fetch［难道正常的加载就不是等有空之后就处理吗？？？？］

事实上，大多数JS和CSS指定的资源声明，对于解析器是不可见的，因而会带来性能损失。
我们可以用preload link来显示地指出哪个资源是user agent必须fetch early的，从而来提高性能，并且这并不阻塞parser和load event。

注意：跨域资源共享（CORS）links，比如fonts或者images，必须带有crossorigin属性，这样才能正确地使用该想资源。
```sh
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
```
对字体的提前加载：web字体是较晚才能被发现的关键资源中常见一类，对文字渲染至关重要，却被深埋在CSS中。crossorigin属性必须的，即使字体资源在自家服务器上。因为用户代理必须采用匿名模式来获取字体资源。［代证！！！！］
type属性可以确保浏览器只获取自己支持的资源。

####早期资源获取和应用的执行
～～～～动态加载，但不执行～～～～
<!-- 1.资源加载成功时，是否立即执行 -->
2.保证在应用中以特有的顺序执行
3.有条件地基于特意的资源或者应用标准来执行
4.推迟资源应用直到满足某些条件（比如下面栗子中的执行脚本代码）
加载脚本
```sh
<script>
    var link = document.createElement("link");
    link.href = "./myscript.js"
    link.rel = "preload";
    link.as = "script";
    document.head.appendChild(link);
</script>
```
执行脚本
```sh
<script>
    var script = document.createElement("script");
    script.src = "./myscript.js"
    document.body.appendChild(script);
</script>
```

～～～～～基于标记语言的异步加载～～～～～
```sh
<link rel="preload" as="style" href="asyncstyle.css" onload="this.rel='stylesheet'" > 
```
上面这个例子中，preload的onload事件可以在资源加载完成后修改rel属性，从而实现异步加载，脚本同理
async虽好，却会阻塞window onload。

使用场景：统计页面访问代码，可以用 preload。
```sh
<link rel="preload" as="script" href="asyncscript.js" onload="var script=document.crateElement('script');script.src=this.href; document.body.appendChild(script);" > 
```

～～～～响应式加载～～～～～
有一个media属性（现在chrome还不支持，［待验证！！！］），该属性似的选择性加载成为可能
移动端盒桌面端，高密度和低密度显示屏的访问，都让我们头疼资源的加载，难道2种活着多种资源都加载么，
最常见的是通过CSS或者JS判断当前浏览器类型动态加载，这样，浏览器的预加载器久无法及时发现他们（浏览器预加载器：只能发现检索到HTML标签中的URL，无法检测到使用脚本代码添加的URL），可能耽误加载时机，影响用户体验。
```sh
<link rel="preload" as="image" href="map.png" media="(max-width:600px)">
<link rel="preload" as="script" href="map.js" media="(max-width:700px)">
```

～～～～HTTP头～～～～
preload还有一个特性是可以通过HTTP头信息呗呈现。也就是上文中大部分的机遇标记语言的声明都可以通过HTTP相应头实现。（唯一的例外是有onload和onerror时间的例子，我们不可能在HTTP头信息中定义事件处理函数）
```sh
Link: <thing_to_load.js>lrel="preload";as="script";
Link: <thing_to_load.woff>;rel="preload";as="font";crossorigin
```
这一方式在有些场景比较实用，比如，当负责优化的人员和页面开发人员不是同一人；外部优化引擎（External optimization engine），该引擎对内容进行扫描并优化。［待研究～～～～～］

～～～～特征检查 Feature Detection～～～～～
判断浏览器是否支持preload，我们修改了ODM规范从而能够得知rel支持哪些值（是否支持rel="reload"）
```sh
var DOMTokenListSupports = function(tokenLidt, token){
    if (!tokenList || !tokenList.supports) {
        return;
    }
    try {
        return tokenList.supports(token);
    } catch (e) {
        if (e instanceof TypeErorr) {
            console.log("The DOMTokenList doesn't have a suppoted tokens list");
        } else {
            console.error("That shoudn't have happend");
        }
    }
};
var linkSupportsPreload = DOMTokenListSupports(document.createElement("link").relList, "preload");
if(!linkSupportsPreload) {
    //Dynamically load the things that relied on prelaod.
}
```

～～～～是否可以用HTTP/2 Push 完成preload工作 ～～～～
是互补而不是取代
HTTP/2 Push的优势是能够主动推送资源给浏览器。
preload的优势是加载过程是透明的，一旦资源加载完毕或者出现异常，应用可以获得事件通知；能加载第三方资源，HTTP/2 Push不能；可以进行内容协商（content negotiaton），可以通过Client－Hints或者HTTP头的信息获取最合适的资源，但是HTTP/2Push不能
HTTP/2 Push： 不能讲浏览器的缓存和非全局cookie（non-global cookie）考虑进去。导致服务器推送的内容可能已经存在客户端缓存中，造成毫无意义的网络传输。



####Devevloper server, and proxy-initated fetching
preload link可以是开发者指定，应用服务自动创建，或者通过一个最优代理（例如CDN）


####待验证的
1.preload 低优先级，确保页面原始资源的加载和执行，以避免正常资源加载的延迟。。［！！待验证］
2.preload不会阻塞windows onload事件，除非preload资源请求刚好来自于会阻塞window 加载的资源 ［待证～～～～］
3.有一个media属性（现在chrome还不支持，［待验证！！！］）











 