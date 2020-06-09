# node.js笔记

## fs

内置的文件系统模块，负责读取文件

### 读取文件

```javascript
var fs = require("fs");

// 异步读取
fs.readFile('input.txt', function (err, data) {
   if (err) {
       return console.error(err);
   }
   console.log("异步读取: " + data.toString());
});

// 同步读取
var data = fs.readFileSync('input.txt');
console.log("同步读取: " + data.toString());

console.log("程序执行完毕。");
```

### 写入文件

```javascript
var fs = require("fs");

console.log("准备写入文件");
fs.writeFile('input.txt', '我是通 过fs.writeFile 写入文件的内容',  function(err) {
   if (err) {
       return console.error(err);
   }
   console.log("数据写入成功！");
   console.log("--------我是分割线-------------")
   console.log("读取写入的数据！");
   fs.readFile('input.txt', function (err, data) {
      if (err) {
         return console.error(err);
      }
      console.log("异步读取文件数据: " + data.toString());
   });
});
```

### 获取文件信息

``fs.stat()``

获取文件大小创建时间等

### 截取文件

- **fd** - 通过 fs.open() 方法返回的文件描述符。
- **len** - 文件内容截取的长度。
- **callback** - 回调函数，没有参数。

```javascript
fs.ftruncate(fd, len, callback)
```



## Stream流

流是一个对象

- **data**表示流的数据已经可以读取了
- **end**表示流已经在末尾了，没有数据可以读取了
- **error**出错

### 从文件流读取

```javascript
'use strict';

var fs = require('fs');

// 打开一个流:
var rs = fs.createReadStream('sample.txt', 'utf-8');
rs.on('data', function (chunk) {
    console.log('DATA:')
    console.log(chunk);
});
rs.on('end', function () {
    console.log('END');
});
rs.on('error', function (err) {
    console.log('ERROR: ' + err);
});
```

### 向文件流写入

```javascript
'use strict';

var fs = require('fs');
var ws1 = fs.createWriteStream('output1.txt', 'utf-8');
ws1.write('使用Stream写入文本数据...\n');
ws1.write('END.');
ws1.end();
var ws2 = fs.createWriteStream('output2.txt');
ws2.write(new Buffer('使用Stream写入二进制数据...\n', 'utf-8'));
ws2.write(new Buffer('END.', 'utf-8'));
ws2.end();
```

### 复制文件流

``pipe()``把一个文件流和另一个文件流串起来，这样源文件的所有数据就自动写入到目标文件里了，所以，这实际上是一个复制文件的程序：

```javascript
'use strict';

var fs = require('fs');

var rs = fs.createReadStream('sample.txt');
var ws = fs.createWriteStream('copied.txt');

rs.pipe(ws);
```





## http

### http请求

#### 发送请求

- 方法（POST/GET)
- 路径
- 域名

#### 返回响应

- 代码（200 404 500）

- 类型（Content-Type）

### http服务器

```javascript
'use strict';

// 导入http模块:
var http = require('http');

// 创建http server，并传入回调函数:
var server = http.createServer(function (request, response) {
    // 回调函数接收request和response对象,
    // 获得HTTP请求的method和url:
    console.log(request.method + ': ' + request.url);
    // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
    response.writeHead(200, {'Content-Type': 'text/html'});
    // 将HTTP响应的HTML内容写入response:
    response.end('<h1>Hello world!</h1>');
});

// 让服务器监听8080端口:
server.listen(8080);
```



一个手写服务器

```javascript
'use strict';
var http = require('http');
var fs = require('fs');
var url = require('url');
// 创建服务器
http.createServer( function (request, response) {  
   // 解析请求，包括文件名
   var pathname = url.parse(request.url).pathname;
   // 输出请求的文件名
   console.log("Request for " + pathname + " received.");
   // 从文件系统中读取请求的文件内容
   fs.readFile(pathname.substr(1),'utf-8', function (err, data) {
​      if (err) {
​         console.log(err);
​         // HTTP 状态码: 404 : NOT FOUND
​         // Content Type: text/plain
​         response.writeHead(404, {'Content-Type': 'text/html','charset':'utf-8'});
​      }else{             
​         // HTTP 状态码: 200 : OK
​         // Content Type: text/plain
​         response.writeHead(200, {'Content-Type': 'text/html' ,'charset':'utf-8'});    
​     
​         // 响应文件内容
​         response.write(data.toString());        
​      }
​      //  发送响应数据
​      response.end();
   });  
}).listen(8080);
```



