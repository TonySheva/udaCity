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
