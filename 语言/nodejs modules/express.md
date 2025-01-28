#### express

`npm i express`

```JavaScript
// 监听 GET/POST 请求
app.get('/user', (req, res) => {
    res.send({ name: 'ivy', age: 20, gender: 'female' })
})
app.get('/user/:ids', (req, res) => {
    res.send(req.params)
})
app.post('/user', (req, res) => {
    res.send('请求成功')
})

// 设置静态文件目录
app.use(express.static('./clock'))
// 对某路径提供静态资源
app.use('/path', express.static('./files'))

// xxx路由.js
const router = express.Router()
router.get('/user/list', (req, res) => {})
router.post('/user/add', (req, res) => {})
// main.js
const router = require('./xxx路由.js')
app.use('/api', router)

// 局部生效中间件
const mwf = (req, res, next) => {
    console.log('调用了局部生效的中间件')
    next()
}
app.get('/', [mwf], (req, res) => {
    res.send('Home Page')
})

// 错误处理中间件
app.use((err, req, res, next) => {
    res.send({ code: 500, msg: err.message })
})

// json | urlencoded
// 除了错误处理中间件，其他的中间件必须在路由之前进行配置
// 解析表单JSON 
app.use(express.json())
// 解析 x-www-form-urlencoded 格式表单数据，配置前 req.body 不可用
app.use(express.urlencoded())

// 跨域问题
const cors = require('cors')
app.use(cors())
```

#### express-session

`npm i express-session`

```JavaScript
////////// 配置 Session 中间件 //////////
const session = require('express-session')
app.use(
	session({
		secret: 'test', // 可以任意写
		resave: false, // 固定写法
		saveUninitialized: true, // 固定写法
	})
)
////////// 向Session中存数据 //////////
// Session中间件配置成功后，req会有session属性
// 使用req.session访问和使用Session对象
app.post('login', (req, res) => {
    req.session.user = "..."
    res.send({ status: 0, msg: 'ok' })
})
app.post('logout', (req, res) => {
    req.session.destroy() // 清空Session消息
    res.send({ status: 0, msg: 'ok' })
})
```

#### express-jwt

`npm i jsonwebtoken express-jwt`

```JavaScript
const jwt = require('jsonwebtoken') // 用于生成JWT字符串
const express_jwt = require('express-jwt') // 用于解析JWT字符串
const secretKey = 'secret' // 定义密钥
///// 注册可以将 JWT字符串 解析成 JSON 的中间件
///// unless 指定哪些接口不需要访问权限
app.use(express_jwt({ secret: secretKey }).unless({ path: [/^\/api\//] }))
///// 登录
app.post('/api/login', () => {
    // code...
    // 生成token发送给客服端
    const token = jwt.sign({ username: '...' }, secretKey, { expiresIn: '24h' })
    res.send({ status: 200, token })
})

////////// 捕获 JWT解析失败 的错误 //////////
// 不捕获错误，程序会因为token过期、token解析失败等而崩溃
app.use((err, req, res, next) => {
    if(err.name === 'UnauthorizedError') {
        return res.send({ status: 401, message: '无效的token' })
    }
    res.send({ status: 500, message: '未知的错误' })
})
```
