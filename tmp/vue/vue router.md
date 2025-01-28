#### views 与 components

- 和路由相关的组件放到  views 中
- views 中存放的是页面组件，配合路由使用
- components 中存放的是复用组件

#### 路由初始化

> 先安装 vue-router 依赖包

- router4

```javascript
// 在 some_router.js 中
import { createRouter } from 'vue-router'
const router = createRouter()
export default router

// 在 main.js 中
import router from '@/router/some_router.js'
app.use(router)
```

- router3

```Javascript
// 在 some_router.js 中
import VueRouter from 'vue-router'
const router = new VueRouter()
export default router

// 在 main.js 中
import router from '@/router/some_router.js'
app.use(router)
```

#### 路由模式

- router4

```javascript
import { createRouter, createWebHistory } from 'vue-router'
const router = createRouter({
	// createWebHistory 对应 history 路由
	// createWebHashHistory 对应 hash 路由
	// 可以在 vite 配置文件中添加配置项 base: 'xxx'，这样就以 '/xxx' 为根目录
	history: createWebHistory(import.meta.env.BASE_URL),
	// 可以直接写 'xxx'，这样就以 'xxx' 为根目录
	history: createWebHistory('xxx')
})
```

- router3

```Javascript
// 1. hash 路由（缺省）, 如 http://localhost:8080/#/home
// 2. history 路由（常用）, 如 http://localhost:8080/home
const router = new VueRouter({
	mode: 'history'
})
```

#### 路由规则

```javascript
const router = new VueRouter({
	routes: [
		{
			// path 是 URI
			path: '/path0',
			component: 对应路径的组件,
			// 组件懒加载的方法
			component: () => import('xxx.vue')
			// 子路由
			children: [{ ... }]
		}
	]
})
```

#### 导航和出口

```html
<!-- 配置导航 -->
<a href='#/path0'>跳转</a>

<!-- 设置路由出口：匹配的组件所展示的位置 -->
<router-view></router-view>
```

#### 跳转与传参

- router4

```html
<script setup>
// 在 setup 语法糖中没有 this
// 需要使用 useRouter 获取 router 对象
// 需要使用 useRoute 获取 route 对象
import { useRoute, useRouter } from 'vue-router'
</script>
```

- router3

```html
<template>
<button @click='handleSearch'>搜索</button>
</template>

<script>
..., 
methods: {
	handleSearch () {
		// 带 query 的跳转 1
		// 接收时使用 $route.query.key
		this.$router.push('/xxx?k1=v1&k2=v2')
		
		// 带 query 的跳转 2
		// 接收时使用 $route.query.key
		this.$router.push({
			path: '/xxx',
			query: { k: v }
		})


		// 动态路由传参需要配置动态路由
		
		// 动态路由传参 1
		// 接收时使用 $route.params.key
		this.$router.push('/xxx/val')
		
		// 动态路由传参 2
		// 接收时使用 $route.params.key
		this.$router.push({
			path: '/xxx/参数值'
		})
		
		
		// 命名路由跳转，适合路径长的场景
		// 为一个路由起名的配置方法: { path: '/xxxxx', name: 'xxx' }
		
		// 命名路由跳转
		this.$router.push({
			name: 'xxx',
			query: {
				k: v
			}
		})

		// 动态命名路由跳转（需要配置动态路由）
		this.$router.push({
			name: '路由名',
			params: {
				k: v
			}
		})
	}
}
</script>
```

#### 重定向

```Javascript
const router = new VueRouter({
	routes: [
	// 用户进入根目录后，会直接跳转到 /home
	{ path: '/', redirect: '/home' },
	// 配置 404 路由，要放在路由列表的最后
	{ path: '*', component: NotFound }
})
```

#### router-link（声明式导航）

> vue-router 提供的一个全局组件，用来取代 a 标签，本质上还是 a 标签

```html
<div class='xxxWrap'>
	<router-link to='/xxx'></router-link>
</div>
<!--
// 会提供高亮类名，用以设置高亮样式
// 提供 router-link-active 类名，模糊匹配，/pathX 开头的路径都能匹配到
.xxxWrap a.router-link-active {
	...
}
// 提供 router-link-exact-active 类名，精确匹配，只能匹配 /pathX 路径
-->
// 如果觉得这两个类名太长，可以配置
const router = new VueRouter({
	linkActiveClass: '类名',
	linkExactActiveClass: '类名'
})

// 声明式导航跳转传参
// 方式 1. 查询参数传参，适合用于多个参数的传递
<router-link to='pathX?参数1=值1&参数2=值2'></router-link>
// 使用 $route.query.参数名 获得参数值
// 在 created 中可以通过 this 获取 $route，用以发送异步请求
// 方式 2. 动态路由传参，适合用于单个参数的传递
// a. 配置动态路由
const router = new VueRouter({
	routes: [
		// '?' 代表 words 参数是可选的，为空时也会匹配到
		{ path: 'search/:words?', component: ... }
	]
})
// b. 配置导航链接
to='/search/参数值'
// c. 通过 $route.params.words 接收
```

#### keep-alive

```html
<!-- 
路由跳转会导致组件销毁，如果希望返回路由时原页面保持原状
可以使用 keep-alive，将原来的组件缓存下来

只缓存组件名0
除了组件名1，其他都缓存
最多缓存 a 个组件
-->
<div class="wrapper" :include=['组件0'] :exclude=['组件1'] :max=a>
	<keep-alive>
		<router-view>...</router-view>
	</keep-alive>
</div>
<!--
被缓存的组件会增加两个生命周期钩子：actived、deactived
actived：激活时，进入页面
deactived：失活时，离开页面
-->
```
