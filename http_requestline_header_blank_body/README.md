HTTP是一个属于应用层的面向对象的协议，由于其简捷、快速的方式，适用于分布式超媒体信息系统
        http（超文本传输协议）是一个基于请求与响应模式的、无状态的、应用层的协议，常基于TCP的连接方式，
        http请求由三部分组成，分别是：请求行、消息报头、请求正文
        HTTP响应也是由三个部分组成，分别是：状态行、消息报头、响应正文
HTTP1.1版本中给出一种持续连接的机制，绝大多数的Web开发，都是构建在HTTP协议之上的Web应用
        面试官给出的问题是 ：
        http协议请求头都有哪些：
        http 分为两部分：请求和响应
        HTTP请求的格式如下所示：
        <request-line>
        <headers>
        <blank line>
        [<request-body>]
        在HTTP请求中，第一行必须是一个请求行（request line），用来说明请求类型、要访问的资源以及使用的HTTP版本。紧接着是一个首部（header）小节，用来说明服务器要使用的附加信息。在首部之后是一个空行，再此之后可以添加任意的其他数据[称之为主体（body）]。
        在HTTP中，定义了多种请求类型，通常我们关心的只有GET请求和POST请求。只要在Web浏览器上输入一个URL，浏览器就将基于该URL向服务器发送一个GET请求，以告诉服务器获取并返回什么资源
        下面我们看一个GET请求：
        对于www.baidu.com的GET请求如下所示：
        GET / HTTP/1.1
        Host: www.baidu.com
        User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
        Gecko/20050225 Firefox/1.0.1
        Connection: Keep-Alive

        请求行的第一部分说明了该请求是GET请求。该行的第二部分是一个斜杠（/），用来说明请求的是该域名的根目录。该行的最后一部分说明使用的是HTTP 1.1版本（另一个可选项是1.0）。那么请求发到哪里去呢？这就是第二行的内容。
        第2行是请求的第一个首部，HOST。首部HOST将指出请求的目的地。结合HOST和上一行中的斜杠（/），可以通知服务器请求的是www.baidu.com/（HTTP 1.1才需要使用首部HOST，而原来的1.0版本则不需要使用）。第三行中包含的是首部User-Agent，服务器端和客户端脚本都能够访问它，它是浏览器类型检测逻辑的重要基础。该信息由你使用的浏览器来定义（在本例中是Firefox 1.0.1），并且在每个请求中将自动发送。最后一行是首部Connection，通常将浏览器操作设置为Keep-Alive（当然也可以设置为其他值）。注意，在最后一个首部之后有一个空行。即使不存在请求主体，这个空行也是必需的。
        要发送GET请求的参数，则必须将这些额外的信息附在URL本身的后面。其格式类似于：
        URL ? name1=value1&name2=value2&..&nameN=valueN

        下面我们再简要的看一下另外一种请求方式：
        POST请求在请求主体中为服务器提供了一些附加的信息。通常，当填写一个在线表单并提交它时，这些填入的数据将以POST请求的方式发送给服务器。
        以下就是一个典型的POST请求：
        POST / HTTP/1.1
        Host: www.baidu.com
        User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
        Gecko/20050225 Firefox/1.0.1
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 40
        Connection: Keep-Alive
        name=Professional%20Ajax&publisher=Wiley
        从上面可以发现， POST请求和GET请求之间有一些区别。首先，请求行开始处的GET改为了POST，以表示不同的请求类型。你会发现首部Host和User-Agent仍然存在，在后面有两个新行。其中首部Content-Type说明了请求主体的内容是如何编码的。浏览器始终以application/ x-www-form- urlencoded的格式编码来传送数据，这是针对简单URL编码的MIME类型。首部Content-Length说明了请求主体的字节数。在首部Connection后是一个空行，再后面就是请求主体。
        
HTTP响应
        如下所示，HTTP响应的格式与请求的格式十分类似：
        <status-line>
        <headers>
        <blank line>
        [<response-body>]
        正如你所见，在响应中唯一真正的区别在于第一行中用状态信息代替了请求信息。状态行（status line）通过提供一个状态码来说明所请求的资源情况。以下就是一个HTTP响应的例子：
        HTTP/1.1 200 OK
Date: Sat, 31 Dec 2005 23:59:59 GMT
Content-Type: text/html;charset=ISO-8859-1
Content-Length: 122
        <html>
        <head>
        <title>Wrox Homepage</title>
        </head>
        <body>
        <!-- body goes here -->
        </body>
        </html>
        在本例中，状态行给出的HTTP状态代码是200，以及消息OK。

常见状态码信息       
状态行格式如下：
HTTP-Version Status-Code Reason-Phrase CRLF
其中，HTTP-Version表示服务器HTTP协议的版本；Status-Code表示服务器发回的响应状态代码；Reason-Phrase表示状态代码的文本描述。
状态代码有三位数字组成，第一个数字定义了响应的类别，且有五种可能取值：
1xx：指示信息--表示请求已接收，继续处理
2xx：成功--表示请求已被成功接收、理解、接受
3xx：重定向--要完成请求必须进行更进一步的操作
4xx：客户端错误--请求有语法错误或请求无法实现
5xx：服务器端错误--服务器未能实现合法的请求
常见状态代码、状态描述、说明：
200 OK      //客户端请求成功
400 Bad Request  //客户端请求有语法错误，不能被服务器所理解
401 Unauthorized //请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
403 Forbidden  //服务器收到请求，但是拒绝提供服务
404 Not Found  //请求资源不存在，eg：输入了错误的URL
500 Internal Server Error //服务器发生不可预期的错误
503 Server Unavailable  //服务器当前不能处理客户端的请求，一段时间后可能恢复正常
eg：HTTP/1.1 200 OK （CRLF）
