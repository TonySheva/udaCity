Fetch
fetch是一个更好的网络API，它由标准委员会提出，并已经在Chrome中实现。它在React Native中也默认可以使用。

使用方法

fetch('https://mywebsite.com/endpoint/')
第二个参数对象是可选的，它可以自定义HTTP请求中的一些部分。

fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  })
})
译注：如果你的服务器无法识别上面POST的数据格式，那么可以尝试传统的form格式，代码如下：

fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
  },
  body: 'key1=value1&key2=value2'
})
异步操作

fetch的返回值是一个Promise对象，你可以用两种办法来使用它：

使用then和catch指定回调函数：
fetch('https://mywebsite.com/endpoint.php')
  .then((response) => response.text())
  .then((responseText) => {
    console.log(responseText);
  })
  .catch((error) => {
    console.warn(error);
  });
使用ES7的async/await语法来发起一个异步调用：
async getUsersFromApi() {
  try {
    let response = await fetch('https://mywebsite.com/endpoint/');
    return response.users;
  } catch(error) {
    throw error;
  }
}
注意：Promise可能会被拒绝，此时会抛出一个错误。你需要自己catch这个错误，否则可能没有任何提示。
WebSocket
WebSocket是一种基于TCP连接的全双工通讯协议。

var ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
  // 建立连接
  ws.send('something');
};

ws.onmessage = (e) => {
  // 收到了消息
  console.log(e.data);
};

ws.onerror = (e) => {
  // 有错误发生
  console.log(e.message);
};

ws.onclose = (e) => {
  // 连接关闭
  console.log(e.code, e.reason);
};
XMLHttpRequest
XMLHttpRequest基于iOS网络API实现。需要注意与网页环境不同的地方就是安全机制：你可以访问任何网站，没有跨域的限制。

var request = new XMLHttpRequest();
request.onreadystatechange = (e) => {
  if (request.readyState !== 4) {
    return;
  }

  if (request.status === 200) {
    console.log('success', request.responseText);
  } else {
    console.warn('error');
  }
};

request.open('GET', 'https://mywebsite.com/endpoint.php');
request.send();
也可以这样用：

var request = new XMLHttpRequest();

function onLoad() {
    console.log(request.status);
    console.log(request.responseText);
};

function onTimeout() {
    console.log('Timeout');
    console.log(request.responseText);
};

function onError() {
    console.log('General network error');
    console.log(request.responseText);
};

request.onload = onLoad;
request.ontimeout = onTimeout;
request.onerror = onError;
request.open('GET', 'https://mywebsite.com/endpoint.php');
request.send();

fetch("/data.json").then(function(res) {
  // res instanceof Response == true.
  if (res.ok) {
    res.json().then(function(data) {
      console.log(data.entries);
    });
  } else {
    console.log("Looks like the response wasn't perfect, got status", res.status);
  }
}, function(e) {
  console.log("Fetch failed!", e);
});
如果是提交一个POST请求，代码如下：

fetch("http://www.example.org/submit.php", {
  method: "POST",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded"
  },
  body: "firstName=Nikhil&favColor=blue&password=easytoguess"
}).then(function(res) {
  if (res.ok) {
    alert("Perfect! Your settings are saved.");
  } else if (res.status == 401) {
    alert("Oops! You are not authorized.");
  }
}, function(e) {
  alert("Error submitting form!");
});
fetch()函数的参数和传给Request()构造函数的参数保持完全一致，所以你可以直接传任意复杂的request请求给fetch()。

Headers

Fetch引入了3个接口，它们分别是 Headers,Request 以及 Response 。他们直接对应了相应的HTTP概念，但是基于安全考虑，有些区别，例如支持CORS规则以及保证cookies不能被第三方获取。

Headers接口是一个简单的多映射的名-值表

var content = "Hello World";
var reqHeaders = new Headers();
reqHeaders.append("Content-Type", "text/plain"
reqHeaders.append("Content-Length", content.length.toString());
reqHeaders.append("X-Custom-Header", "ProcessThisImmediately");
也可以传一个多维数组或者json：

reqHeaders = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});
Headers的内容可以被检索：

console.log(reqHeaders.has("Content-Type")); // true
console.log(reqHeaders.has("Set-Cookie")); // false
reqHeaders.set("Content-Type", "text/html");
reqHeaders.append("X-Custom-Header", "AnotherValue");

console.log(reqHeaders.get("Content-Length")); // 11
console.log(reqHeaders.getAll("X-Custom-Header")); // ["ProcessThisImmediately", "AnotherValue"]

reqHeaders.delete("X-Custom-Header");
console.log(reqHeaders.getAll("X-Custom-Header")); // []
一些操作不仅仅对ServiceWorkers有用，本身也提供了更方便的操作Headers的API（相对于XMLHttpRequest来说——译者注）。

由于Headers可以在request请求中被发送或者在response请求中被接收，并且规定了哪些参数是可写的，Headers对象有一个特殊的guard属性。这个属性没有暴露给Web，但是它影响到哪些内容可以在Headers对象中被改变。

可能的值如下：

"none": 默认的
"request": 从Request中获得的Headers只读。
"request-no-cors"：从不同域的Request中获得的Headers只读。
"response": 从Response获得的Headers只读。
"immutable" 在ServiceWorkers中最常用的，所有的Headers都只读。
哪一种 guard 作用于 Headers 导致什么行为，详细定义在了这个规范中。例如，你不可以添加或者修改一个guard属性是"request"的Request Headers的"Content-Length"属性。同样地，插入"Set-Cookie"属性到一个Response headers是不允许的，因此ServiceWorkers是不能给合成的Response的headers添加一些cookies。

如果使用了一个不合法的HTTP Header属性名，那么Headers的方法通常都抛出 TypeError 异常。如果不小心写入了一个不可写的属性，也会抛出一个 TypeError 异常。除此以外的情况，失败了并不抛出异常。例如：

var res = Response.error();
try {
  res.headers.set("Origin", "http://mybank.com");
} catch(e) {
  console.log("Cannot pretend to be a bank!");
}
Request

Request接口定义了通过HTTP请求资源的request格式。参数需要URL、method和headers，同时Request也接受一个特定的body，mode，credentials以及cache hints.

最简单的 Request 当然是一个URL，可以通过URL来GET一个资源。

var req = new Request("/index.html");
console.log(req.method); // "GET"
console.log(req.url); // "http://example.com/index.html"
你也可以将一个建好的Request对象传给构造函数，这样将复制出一个新的Request。

var copy = new Request(req);
console.log(copy.method); // "GET"
console.log(copy.url); // "http://example.com/index.html"
这种用法通常见于ServiceWorkers。

URL以外的其他属性的初始值能够通过第二个参数传给Request构造函数。这个参数是一个json：

var uploadReq = new Request("/uploadImage", {
  method: "POST",
  headers: {
    "Content-Type": "image/png",
  },
  body: "image data"
});
mode属性用来决定是否允许跨域请求，以及哪些response属性可读。可选的mode属性值为same-origin，no-cors（默认）以及cors。

same-origin模式很简单，如果一个请求是跨域的，那么返回一个简单的error，这样确保所有的请求遵守同源策略。

var arbitraryUrl = document.getElementById("url-input").value;
fetch(arbitraryUrl, { mode: "same-origin" }).then(function(res) {
  console.log("Response succeeded?", res.ok);
}, function(e) {
  console.log("Please enter a same-origin URL!");
});
no-cors模式允许来自CDN的脚本、其他域的图片和其他一些跨域资源，但是首先有个前提条件，就是请求的method只能是"HEAD","GET"或者"POST"。此外，任何 ServiceWorkers 拦截了这些请求，它不能随意添加或者改写任何headers，除了这些。第三，JavaScript不能访问Response中的任何属性，这保证了 ServiceWorkers 不会导致任何跨域下的安全问题而隐私信息泄漏。

cors模式我们通常用作跨域请求来从第三方提供的API获取数据。这个模式遵守CORS协议。只有有限的一些headers被暴露给Response对象，但是body是可读的。例如，你可以获得一个Flickr的最感兴趣的照片的清单：

var u = new URLSearchParams();
u.append('method', 'flickr.interestingness.getList');
u.append('api_key', '<insert api key here>');
u.append('format', 'json');
u.append('nojsoncallback', '1');

var apiCall = fetch('https://api.flickr.com/services/rest?' + u);

apiCall.then(function(response) {
  return response.json().then(function(json) {
    // photo is a list of photos.
    return json.photos.photo;
  });
}).then(function(photos) {
  photos.forEach(function(photo) {
    console.log(photo.title);
  });
});
你无法从Headers中读取"Date"属性，因为Flickr在Access-Control-Expose-Headers中设置了不允许读取它。

response.headers.get("Date"); // null
credentials枚举属性决定了cookies是否能跨域得到。这个属性与XHR的withCredentials标志相同，但是只有三个值，分别是"omit"（默认）,"same-origin"以及"include"。

Request对象也可以提供 caching hints 给用户代理。这个属性还在安全复审阶段。Firefox 提供了这个属性，但是它目前还不起作用。

Request还有两个只读的属性与ServiceWorks拦截有关。其中一个是referrer，表示Request的来源，可能为空。另外一个是context，是一个非常大的枚举集合定义了获得的资源的种类，它可能是image比如请求来自于img标签，可能是worker如果是一个worker脚本，等等。如果使用fetch()函数，这个值是fetch。

Response

Response实例通常在fetch()的回调中获得。但是它们也可以用JS构造，不过通常这招只用于ServiceWorkers。

Response中最常见的成员是status（一个整数默认值是200）和statusText（默认值是"OK"），对应HTTP请求的status和reason。还有一个"ok"属性，当status为2xx的时候它是true。

headers 属性是Response的Headers对象，它是只读的(with guard "response")，url属性是当前Response的来源URL。

Response 也有一个type属性，它的值可能是"basic","cors","default","error"或者"opaque。

"basic": 正常的，同域的请求，包含所有的headers除开"Set-Cookie"和"Set-Cookie2"。
"cors": Response从一个合法的跨域请求获得，一部分header和body可读。
"error": 网络错误。Response的status是0，Headers是空的并且不可写。当Response是从Response.error()中得到时，就是这种类型。
"opaque": Response从"no-cors"请求了跨域资源。依靠Server端来做限制。
"error"类型会导致fetch()函数的Promise被reject并回调出一个TypeError。

还有一些属性只在ServerWorker作用域下有效。以正确的方式 返回一个Response针对一个被ServiceWorkers拦截的Request，可以像下面这样写：

addEventListener('fetch', function(event) {
  event.respondWith(new Response("Response body", {
    headers: { "Content-Type" : "text/plain" }
  });
});
如你所见，Response有个接收两个可选参数的构造器。第一个参数是返回的body，第二个参数是一个json，设置status、statusText以及headers。

静态方法Response.error()简单返回一个错误的请求。类似的，Response.redirect(url, status)返回一个跳转URL的请求。

处理body

无论Request还是Response都可能带着body。由于body可以是各种类型，比较复杂，所以前面我们故意先略过它，在这里单独拿出来讲解。

body可以是以下任何一种类型的实例：

ArrayBuffer
ArrayBufferView(Uint8Array and friends)
Blob/File
字符串
URLSearchParams
FormData——目前不被Gecko和Blink支持，Firefox预计在版本39和Fetch的其他部分一起推出。
此外，Request和Response都为他们的body提供了以下方法，这些方法都返回一个Promise对象。

arrayBuffer()
blob()
json()
text()
formData()
在使用非文本的数据方面，Fetch API和XHR相比提供了极大的便利。

可以通过传body参数来设置Request的body：

var form = new FormData(document.getElementById('login-form'));
fetch("/login", {
  method: "POST",
  body: form
})
Response的第一个参数是body：

var res = new Response(new File(["chunk", "chunk"], "archive.zip",
                       { type: "application/zip" }));
Request和Response（通过fetch()方法）都能够自动识别自己的content type，Request还可以自动设置"Content-Type" header，如果开发者没有设置它的话。

流和克隆

非常重要的一点要说明，那就是Request和Response的body只能被读取一次！它们有一个属性叫bodyUsed，读取一次之后设置为true，就不能再读取了。

var res = new Response("one time use");
console.log(res.bodyUsed); // false
res.text().then(function(v) {
  console.log(res.bodyUsed); // true
});
console.log(res.bodyUsed); // true

res.text().catch(function(e) {
  console.log("Tried to read already consumed Response");
});
这样设计的目的是为了之后兼容基于流的API，让应用一次消费data，这样就允许了JavaScript处理大文件例如视频，并且可以支持实时压缩和编辑。

有时候，我们希望多次访问body，例如，你可能想用即将支持的Cache API去缓存Request和Response，以便于可以离线使用，Cache要求body能被再次读取。

所以，我们该如何让body能经得起多次读取呢？API提供了一个clone()方法。调用这个方法可以得到一个克隆对象。不过要记得，clone()必须要在读取之前调用，也就是先clone()再读取。

addEventListener('fetch', function(evt) {
  var sheep = new Response("Dolly");
  console.log(sheep.bodyUsed); // false
  var clone = sheep.clone();
  console.log(clone.bodyUsed); // false

  clone.text();
  console.log(sheep.bodyUsed); // false
  console.log(clone.bodyUsed); // true

  evt.respondWith(cache.add(sheep.clone()).then(function(e) {
    return sheep;
  });
});
