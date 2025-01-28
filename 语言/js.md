#### Promise

```JavaScript
// 回调地狱问题
setTimeout(() => {
    ...
    setTimeout(() => {
        ...
        setTimeout(() => {
            ...
        }, 1000);
    }, 1000);
}, 1000);
// 解决方案：promise
// resolve可以传递值给下一个异步任务
// 调用resolve以完成当前Promise
// 给 reject 传递的实参可以被catch中的err接收
// 调用reject以确定当前Promise失败
new Promise((resolve, reject) => {
    setTimeout(() => {
        console.log('第一次打印');
        resolve('data1') // 将data传递给下一个任务
    }, 1000);
}, err => {
    console.log('第一个任务失败了')
    throw new Error('任务一失败了') // 必须要加这行，不然仍然会返回一个成功的Promise
}).then(res => {
    console.log('这里是从第一个任务中传递来的' + res)
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('第二次打印')
            resolve('data2')
        }, 1000);
    })
}).then(res => {
    console.log('这里是从第二个任务中传递来的' + res)
    setTimeout(() => {
        console.log('第三次打印')
    }, 1000);
}).catch((err) => {
    console.log(err)
}).finally(() => {})
///// Promise.all -> 合并多个Promise对象
const a = axios({ url: '<http://hmajax.itheima.net/api/weather>', params: { city: '110100' } })
const b = axios({ url: '<http://hmajax.itheima.net/api/weather>', params: { city: '310100' } })
const c = axios({ url: '<http://hmajax.itheima.net/api/weather>', params: { city: '440100' } })
const d = axios({ url: '<http://hmajax.itheima.net/api/weather>', params: { city: '440300' } })
const p = Promise.all([a, b, c, d])
p.then(result => {...}) // result中数据与合并时一致
```

#### this 指向问题

- 箭头函数中 this 指向上下文中父级对象
- function 匿名函数中 this 指向调用者
- 箭头函数没有原型属性

#### reflect | 反射

```JavaScript
obj = {
    x: 1,
    y: 2
}
Reflect.get(obj, 'x')
Reflect.set(obj, 'x', 2)
Reflect.deleteProperty(obj, 'x')
// Object的此方法重定义obj会抛出错误
// Reflect的此方法不会抛出错误，但是会返回布尔值，反映了属性是否定义成功
Reflect.defineProperty(obj, 'z', {
    get() {
        return 3
    }
})
```

#### 事件监听

```JavaScript
function func() {
	console.log('hi')
}
// 为窗口添加、移除事件
window.addEventListener('click', func)
window.removeEventListener('click', func)
```

#### 实现响应式的方法

```JavaScript
// 方法一
///// 对象类型的原理
const _data = {name: 'ivy'}
const data = {}
// 通过defineProperty()对属性的读取、修改进行拦截（数据劫持）
Object.defineProperty(data, 'name', {
    get() {return _data.name},
    set(value) {_data.name = value}
    configurable: false // 是否可被delete，默认为false
})
///// 数组类型的原理：一系列方法
///// vue2响应式存在的问题：
// 新增、删除属性，界面不会更新
// 方法二
///// Proxy/代理
let person = {
    name: 'ivy',
    age: 18
}
const p0 = new Proxy(person, {}) // 第二个参数可为空，但不能不写
// 可通过p0获取、设置、删除person的属性
///// vue3使用Proxy/代理实现响应式的原理
const p = new Proxy(person, {
    // 读取属性时调用
    get(target, propName) {
        return Reflect.get(target, propName)
    },
    // 修改、增加属性时调用
    set(target, propName, value) {
        Reflect.set(target, propName, value)
    },
    // 删除属性时调用
    deleteProperty(target, propName) {
        return Reflect.deleteProperty(target, propName) // 返回成功与否
    }
})
```

#### XMLHttpRequest | XHR

```JavaScript
///// 创建 XMLHttpRequest 对象
const xhr = new XMLHttpRequest()
///// 配置请求方法和请求 url 地址
xhr.open('GET', 'http://hmajax.itheima.net/api/province')
///// 监听 loadend 事件，接收响应结果
xhr.addEventListener('loadend', () => {
  console.log(xhr.response)
  const data = JSON.parse(xhr.response)
  console.log(data.list.join('<br>'))
  document.querySelector('.my-p').innerHTML = data.list.join('<br>')
})
///// 发起请求
xhr.send()
```

#### DOM 刷新的一种方法

```JavaScript
let box = document.querySelector('#box')
Object.defineProperty(box, 'name', {
	get() {
		return box.innerText
	},
	set(value) {
		box.innerText = value
	}
})
```

#### 模块化

- 概述
	- 每个模块都是一个文件
	- 模块中的数据都是私有的
- 规范
	- CommonJS
	- ESModule

##### CommonJS

```javascript
// 无论如何，最终真正导出的都是 module.exports 的值

// 模块 1
const a = 1
const b = 1
const c = 1
// 方式 1
module.exports = { a }
// 方式 2
exports.b = b
// 方式 3
this.c = c
// 多种方式不建议混用

// 模块 2
// 不解构
const module1 = require('模块1.js')
// 解构并重命名
const { name: stuName, ... } = require('模块1.js')
```

##### ESModule

```javascript
// 模块 1
export const a = 1
const b = 1
const c = 1
export { b }
export default { c }
// 三种导出方式可以混用

// 模块 2
// 导入所有
import * as module1 from '模块1'
// 导入并命名
import { name as stuName } from '模块1'
```