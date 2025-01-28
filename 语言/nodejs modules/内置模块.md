#### fs

```JavaScript
const fs = require('fs')

fs.readFile('./xxx', 'utf8', (err, content) => {
    // content: 读取到的内容
    // 读取成功，err 为 null
    // 读取失败，err 为错误对象，content 为 undefined
})

content = 'hello, world'
fs.writeFile('./xxx', content, (err) => {
    // 写入成功：err 为 null
    // 写入失败：err 为一个错误对象
})
```

#### path

```JavaScript
const path = require('path')
const fs = require('fs')

// 拼接 __dirname 和一个路径
const dir = path.join(__dirname, './files/test.txt')

const full_path = '/a/b/c/index.html'
// 获取路径的最后一部分，含后缀
const full_name = path.basename(full_path)
// 获取路径的最后一部分，不含指定后缀
const name_without_ext = path.basename(full_name, '.html')

const dir = '/a/b/c/index.html'
// 获取路径的后缀，包括“.”
const extension = path.extname(dir)
```

#### http

```JavaScript
const http = require('http')

// 创建 web 服务器实例
const server = http.createServer()

// 为服务器绑定 request 事件以监听客户端的请求
server.on('request', function (req, res) {
    // 设置响应头，解决乱码
    res.setHeader('Content-Type', 'text/html; charset=utf-8')
    
    if(req.url === '/index.html') {
        content = '<h1>首页</h1>'
    } else if ...
    
    res.end(content) // 发送content响应
})

// 监听 80 端口
server.listen(80, function () {
    ...
})
```