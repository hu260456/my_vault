#### 定义 Store

- actions 相当于 vuex 中的 mutations 和 actions
- pinia 中的 actions 支持编写异步语句

- 组合式

```javascript
// ref() 就是 state
// computed() 就是 getters
// function() 就是 actions
import { defineStore } from 'pinia'
// 其中参数一是仓库的唯一标识
export const useCounterstore = defineStore('counter', () => {
	const count = ref(0)
	
	function increment() {
		count.value++
	}
	
	return { count, increment }
})
```

- 选项式

```javascript
// 你可以认为state是store 的数据（data），getters 是store 的计算属性（computed），而actions则是方法（methods）
export const useCounterStore = defineStore('counter', {
	state: () => {
		count: 0
	},
	getters: {
		doubles: () => state.count * 2;
	},
	actions: {
		incement() {
			this.count++
		}
	}
})
```

#### 使用数据

```html
<script setup>
import { storeToRefs } from 'pinia'
import { useCounterStore } from '@/store/counter'

const counterStore = useCounterStore()
// 属性如果直接解构，会丢失响应式
const { count } = storeToRefs(counterStore)
// actions 可以直接结构
const { increment } = counterStore
</script>

<template>
	<!-- store 是一个用 reactive 包装的的对象，因此无需 .value -->
	<!-- 带仓库前缀 -->
	<div>{{ counterStore.count }}</div>
	<!-- 不带仓库前缀 -->
	<div>{{ count }}</div>
</template>
```

#### 持久化

> `pinia-plugin-persistedstate` 是 pinia 持久化插件

`npm install -D pinia-plugin-persistedstate`

- 将插件添加到 pinia 实例

```javascript
import{ createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'
const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)
```

- 配置仓库持久化 - 组合式

```javascript
import { defineStore } from 'pinia'
// 默认使用 localStorage 进行存储
// 默认使用仓库名作为的 key
// 默认整个 state 被持久化
// 使用 JSON.stringify 和 JSON.parse 进行序列化和反序列化
export const useCounterstore = defineStore('counter', () => { ... }, {
	// 使用默认配置
	persist: true,
	// 使用更精细的配置
	persist: {
		// 自定义 Storage 的 key
		key: 'my-custom-key',
		// 自定义 Storage
		storage: sessionStorage,
		// 自定义需要持久化的数据
		paths: [var1, var2, obj1.prop1, obj2.prop2, obj3]
	}
})
```

- 配置仓库持久化 - 选项式

```javascript
export const useCounterStore = defineStore('counter', {
	state: () => {},
	getters: {},
	actions: {},
	// 使用默认配置
	persist: true,
	// 使用更精细的配置
	persist: { ... }
})
```


