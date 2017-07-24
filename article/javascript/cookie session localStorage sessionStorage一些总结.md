## cookie 和 session
### cookie机制
正统的cookie分发是通过扩展HTTP协议来实现的，服务器通过在HTTP的响应头中加上一行特殊的指示以提示浏览器按照指示生成相应的cookie。
然而纯粹的客户端脚本如JavaScript或者VBScript也可以生成cookie。
而cookie的使用是由浏览器按照一定的原则在后台自动发送给服务器的。浏览器检查所有存储的cookie，如果某个cookie所声明的作用范围大于等于将要请求的资源所在的位置，则把该cookie附在请求资源的HTTP请求头上发送给服务器。
早期的 Web 应用面临的最大问题之一就是如何维持状态。简言之，服务器无法知道两个请求是否来自于同一个浏览器。
### 什么是cookie
实际上大多数的应用都是用 Cookie 来实现Session跟踪的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在 Cookie 里面记录一个Session ID，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。
### 什么是session
当程序需要为某个客户端的请求创建一个session时，服务器首先检查这个客户端的请求里是否已包含了一个session标识（称为session id），如果已包含则说明以前已经为此客户端创建过session，服务器就按照session id把这个session检索出来使用（检索不到，会新建一个），如果客户端请求不包含session id，则为此客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，这个session id将被在本次响应中返回给客户端保存。
Jsessionid只是tomcat的对sessionid的叫法，其实就是sessionid；在其它的容器也许就不叫jsessionid了。
### cookie和session不同

 1. 存取方式的不同
 cookie中只能保管ASCII字符串
 session中能够存取任何类型的数据，包括而不限于String、Integer、List、Map等。Session中也能够直接保管Java Bean乃至任何Java类，对象等，运用起来十分便当。能够把Session看做是一个Java容器类。
 2. 隐私策略的不同
Cookie存储在客户端阅读器中，对客户端是可见的，客户端的一些程序可能会窥探、复制以至修正Cookie中的内容。
而Session存储在服务器上，对客户端是透明的，不存在敏感信息泄露的风险。
假如选用Cookie，比较好的方法是，敏感的信息如账号密码等尽量不要写到Cookie中。最好是像Google、Baidu那样将Cookie信息加密，提交到服务器后再进行解密
 3. 有效期上的不同
 使用过Google的人都晓得，假如登录过Google，则Google的登录信息长期有效。用户不用每次访问都重新登录，Google会持久地记载该用户的登录信息。要到达这种效果，运用Cookie会是比较好的选择。只需要设置Cookie的过期时间属性为一个很大很大的数字。
由于Session依赖于名为JSESSIONID的Cookie，而Cookie JSESSIONID的过期时间默许为–1，只需关闭了阅读器该Session就会失效，因而Session不能完成信息永世有效的效果。
运用URL地址重写也不能完成。而且假如设置Session的超时时间过长，服务器累计的Session就会越多，越容易招致内存溢出。
 4. 服务器压力的不同
 Session是保管在服务器端的，每个用户都会产生一个Session。假如并发访问的用户十分多，会产生十分多的Session，耗费大量的内存。因而像Google、Baidu、Sina这样并发访问量极高的网站，是不太可能运用Session来追踪客户会话的。
而Cookie保管在客户端，不占用服务器资源。假如并发阅读的用户十分多，Cookie是很好的选择。关于Google、Baidu、Sina来说，Cookie或许是唯一的选择。
 5. 浏览器支持的不同
 Cookie是需要客户端浏览器支持的。假如客户端禁用了Cookie，或者不支持Cookie，则会话跟踪会失效。关于WAP上的应用，常规的Cookie就派不上用场了。
假如客户端浏览器不支持Cookie，需要运用Session以及URL地址重写。需要注意的是一切的用到Session程序的URL都要进行URL地址重写，否则Session会话跟踪还会失效。关于WAP应用来说，Session+URL地址重写或许是它唯一的选择。
 6. 跨域支持上的不同
 Cookie支持跨域名访问，例如将domain属性设置为“.biaodianfu.com”，则以“.biaodianfu.com”为后缀的一切域名均能够访问该Cookie。跨域名Cookie如今被普遍用在网络中，例如Google、Baidu、Sina等。
而Session则不会支持跨域名访问。Session仅在他所在的域名内有效。
**session 的运行依赖 session id，而 session id 是存在 cookie 中的，也就是说，如果浏览器禁用了 cookie ，同时 session 也会失效（但是可以通过其它方式实现，比如在 url 中传递 session_id）。因此，维持一个会话的核心就是客户端的唯一标识，即 session id**

### WebStorage
**既然有了cookie本地存储，为什么还要引入WebStorage的概念？**
cookie的缺陷是非常明显的
1. 数据大小：作为存储容器，cookie的大小限制在4KB左右这是非常坑爹的
2. 安全性问题：由于在HTTP请求中的cookie是明文传递的（HTTPS不是），带来的安全性问题还是很大的。
3. 网络负担：我们知道cookie会被附加在每个HTTP请求中，在HttpRequest 和HttpResponse的header中都是要被传输的，所以无形中增加了一些不必要的流量损失。
WebStorage是HTML新增的本地存储解决方案之一，*但并不是为了取代cookie而制定的标准，cookie作为HTTP协议的一部分用来处理客户端和服务器通信是不可或缺的，session正是依赖于实现的客户端状态保持。* 
**WebStorage的意图在于解决本来不应该cookie做，却不得不用cookie的本地存储。**
WebStorage提供两种类型的API：localStorage和sessionStorage，两者的区别看名字就有大概了解，localStorage在本地永久性存储数据，除非显式将其删除或清空，sessionStorage存储的数据只在会话期间有效，关闭浏览器则自动删除。

### 为什么比cookie好
 1. 从容量上讲WebStorage一般浏览器提供5M的存储空间，用来存储视频、图片神马的不够，但对于绝大部分操作足矣
 2. 安全性上WebStorage并不作为HTTP header发送的浏览器，所以相对安全
 3. 从流量上讲，因为WebStorage不传送到服务器，所以不必要的流量可以节省，这样对于高频次访问或者针对手机移动设备的网页还是很不错的。
**这并不意味着WebStorage可以取代cookie，而是有了WebStorage后cookie能只做它应该做的事情了——作为客户端与服务器交互的通道，保持客户端状态。所以仅仅作为本地存储解决方案WebStorage是优于cookie的。**
1. 虽然localStorage存储在本地，但不同的浏览器存储存储数据是独立的，所以在Chrome上存储的localStorage在FireFox上是获取不到的。
2. localStorage和sessionStorage只能存储字符串类型

### localStorage与sessionStorage区别
1. 数据的生命期
localStorage除非被清除，否则永久保存
sessionStorage仅在当前会话下有效，关闭页面或浏览器后被清除，刷新页面数据依旧存在。

### 安全性考虑
不是什么数据都适合放在 Cookie、localStorage 和 sessionStorage 中的。使用它们的时候，需要时刻注意是否有代码存在 XSS 注入的风险。因为只要打开控制台，你就随意修改它们的值，也就是说如果你的网站中有 XSS 的风险，它们就能对你的 localStorage 肆意妄为。所以千万不要用它们存储你系统中的敏感数据。
