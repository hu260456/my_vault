
`npm i axios`

```JavaScript
///// 引入axios
const axios = require('axios').default
///// 发送 GET 请求
axios({ // axois返回一个Promise
    url: '<http://hmajax.itheima.net/api/province>'
}).then(res => {
    // code...
})
///// params/查询参数
axios({
    url: '<http://hmajax.itheima.net/api/city>',
    params: {
        pname: '广东省'
    }
}).then(res => {
    // code...
})
///// 发送 POST 请求
axios({
    url: '<http://hmajax.itheima.net/api/register>',
    method: 'POST',
    data: {
        username: '...', password: '...'
    }
}).then(res => {
    // code
})
///// 异常处理
axios({}).then(func).catch(err => {
    // code
})
```
