
`npm i mysql`

```JavaScript
const mysql = require('mysql')
const db = mysql.createPool({
    host: '127.0.0.1', // 数据库的 IP 地址
    user: 'root', // 登录数据库的账号
    password: '666666', // 登录数据库的密码
    database: 'test', // 指定要操作哪个数据库
})
////////// 测试连接 //////////
db.query('select 1', (err, results) => {
    if(err) return console.log(err.message)
    console.log(results)
})
///// 查询数据库中所有user
const SQL = 'select * from users'
db.query(SQL, (err, results) => {
    if (err) return console.log(err.message)
    // 查询数据成功
    // 注意：如果执行的是 select 查询语句，则执行的结果是数组
    console.log(results)
})
///// insert操作
// 向users中新增一条数据（新增一用户）
const user_info = { username: 'ivy', password: '666666' }
// ?：占位符，查询时需要传递参数
const SQL = 'insert into users (username, password) values (?, ?)'
db.query(SQL, [user_info.username, user_info.password], (err, results) => {
	if (err) return console.log(err.message)
	// 注意：如果执行的是 insert into 插入语句，则 results 是一个对象
	// 可以通过 affectedRows 属性，来判断是否插入数据成功
	if (results.affectedRows === 1) {
		console.log('插入数据成功!')
	}
})
///// insert操作（方言版）
const user = { username: 'ivy', password: '666666' }
const SQL = 'insert into users values ?'
db.query(SQL, user, (err, results) => {
	if (err) return console.log(err.message)
})
///// update
const user = { id: 6, username: 'ivyanne', password: '666666' }
const SQL = 'update users set username=?, password=? where id=?'
db.query(SQL, [user.username, user.password, user.id], (err, results) => {
	if (err) return console.log(err.message)
})
///// update简单版
const user = { id: 6, username: 'ivyanne', password: '666666' }
const SQL = 'update users set ? where id=?'
db.query(SQL, [user, user.id], (err, results) => {
	if (err) return console.log(err.message)
})
```
