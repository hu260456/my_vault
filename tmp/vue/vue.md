#### 指令

#####  v-bind

```Javascript
// 绑定标签属性
v-bind:a="var"
// 简写
:a="var"

// 动态控制 class 属性
:class='{pink: true, big: false}'
let arr = ['pink', 'big']
:class='arr'
:style='{height: "400px", "background-color": "green"}'

// sync 修饰符，用于父子组件的双向数据绑定
// 父组件中
:visible.sync='isShow'
// 等价于
:visible='isShow' @update=visible
// 子组件中
props: {
	visible: Boolean
}
Fn () {
	this.$emit('update:visible', false)
}
```

#####  v-on

```html
// 注册 click 事件
<button v-on:click="func(a)">登录</button>
// 简写
<button @click="func(a)">登录</button>
// 注册键盘弹起事件
<button @keyup="func">登录</button>
// 注册 ENTER 键的弹起事件
<button @keyup.enter="func">登录</button>
// 阻止冒泡
<button @keyup.enter="func" @click.stop>登录</button>
// 阻止默认行为
<button @keyup.enter="func" @click.prevent>登录</button>
```

#####  v-html

> 为 DOM 结点嵌入 HTML 代码

```html
<div v-html='html_var'></div>
```

#####  v-model

> 实际上是一个语法糖，可以实现双向数据绑定，不仅数据驱动视图，视图变化后视图也自动更新

```html
<!-- 去除首尾空格 -->
<input v-model.trim='msg' type='text'>
<!-- 转为数字 -->
<input v-model.number='msg' type='text'>

<!-- 对于输入框 -->
<input v-model='msg' type='text'>
<!-- vue3 中相当于 -->
<input :modelValue='msg' @update:modelValue type='text'>
<!-- vue2 中相当于 -->
<input :value='msg' @input='msg=$event.target.value' type='text'>
```

#####  v-for

```HTML
<table>
	<thead>...</thead>
	<tbody v-for="(item, index) in iList" :key="item">
		<td>...</td>
	</tbody>
</table>
```

##### v-xxx

> 自定义指令

```javascript
// 全局注册
Vue.directive('指令名', {
	// 有 inserted 和 update 两大钩子
	// inserted -> 指令所绑定的元素被添加到页面的时候自动调用
	// el 表示指令所绑定的元素
	'inserted' (el) {
		el.focus()
	}
})

// 局部注册，在组件中进行
directives: {
	'指令名': {
		inserted (el, binding) {
			el.style.color = binding.value
		},
		// update -> 会在指令值修改的时候触发，提供值变化后，DOM 更新的逻辑
		update (el, binding) {
			el.style.color = binding.value
		}
	}
}

// 自定义组件的使用
<Xxx v-指令名></Xxx>
```

#### 函数

##### vue3 响应式对象

```javascript
// 接受一个对象，返回一个响应式对象
const var = reactive(obj)

// 接受简单类型或对象，返回一个响应式对象
const var = ref(obj)
// 访问 ref 对象（在模板中不需要 .value）
console.log(var.value)
```

##### computed

- 插值语法中不加括号
- 不应该存在异步请求
- 不应该修改 DOM
- 计算属性会缓存计算结果，除非依赖项变化，计算属性不会重复计算

- 组合式

```javascript
// 创建一个只读的计算属性
const func = computed(() => {
	return ...
})

// 创建一个可写的计算属性
// 建议只使用只读的计算属性
const func = computed(() => {
	get: () => ...,
	set: (val) => ...
})
```

- 选项式

```Javascript
computed: {
	func () {
		return ...
	}
	
	// 配置可读写的计算属性
	func: {
		get() {
			return ...
		}
		set(val) {
			...
		}
	}
}
```

##### methods

- 插值语法中需要加括号

- 组合式

```javascript
const func = () => {...}
```

- 选项式

```Javascript
methods: {
	func () {...}
}
```

##### watch

- 组合式

```javascript
let count = ref(1)

// 监听单个数据
watch(count, (new_val, old_val) => {...})

// 监听多个数据
watch([var1, var2], ([new1, old1], [new2, old2]) => {...})

// 填写 immediate, deep 配置项
watch(obj, (new_val, old_val) => {...}, {
	immediate: true,
	deep: true
})

// 不开启 deep 配置项，但是需要监听 obj 的 prop 属性
const obj = {
	...,
	prop
}
watch(() => obj.value.prop, (new_val, old_val) => {...})
```

- 选项式

```Javascript
watch: {
	// 监视简单类型
	val (newVal, oldVal) {
		...
	},

	// 监视复杂类型
	obj: {
		// 初始化时立即执行一次
		// immediate: true,
		// 深度监视
		deep: true,
		handler (newVal, oldVal) {
			...
		}
	}
}
```

##### hooks

- 组合式

```javascript
setup(() => {...})
onBeforeMount(() => {...})
onMounted(() => {...})
onBeforeUpdate(() => {...})
onUpdated(() => {...})
onBeforeUnmount(() => {...})
onUnmounted(() => {...})
```

- 选项式

```javascript
beforeCreate () {
	// 响应式数据准备好之前
	// 此段逻辑在组合式 API 中需要写到
},
created () {
	// 响应式数据准备好之后，此时可以发送异步请求
},
beforeMount () {
	// 模板渲染之前
},
mounted () {
	// 模板渲染之后，此时可以操作 DOM
},
beforeUpdate () {
	// 数据修改了，但视图还未更新
},
updated () {
	// 数据修改了，视图也更新了
},
// 可以通过 app.$destroy() 卸载实例而不关闭页面
beforeDestroy () {
	// 卸载 Vue 实例之前
},
destroyed () {
	// Vue 实例卸载后
}
```

#### 组件导入和注册

- 组合式

	组件无需注册，import 后直接使用
	使用 element-plus 的按需导入功能还可以自动 import

- 选项式

```Javascript
// 在组件中局部注册
import 组件名 from '@/components/xxx'
export default {
	components: {
		'组件名': 组件对象 // 名字和对象一样时可以简写
	}
}

// 在 main.js 中全局注册
import 组件名 from '@/components/xxx'
Vue.component('组件名', 组件对象)
```

#### 组件通信

##### 父传子

- 组合式

```javascript
// 父组件中
// <Son message='hello, world'></Son>
// 传递 ref 对象
// <Son :message=obj></Son>

// 子组件中
// 使用 setup 语法糖，无法直接配置 props 配置项，需要使用 defineProps
// 使用 setup 语法糖，脚本中需要使用 props.xxx 引用接收到的数据
// 使用 setup 语法糖，模板中可以直接使用 xxx 接收到的数据
const props = defineProps({
	// simple 写法
	message: String,
	// advanced 写法
	message: {
		type: String,
		required: false,
		default: '666',
		// 自定义校验逻辑
		validator (val) {
			...
			return true;
		}
	}
})
```

- 选项式

```Javascript
// 父组件中
// <Son :data='data'></Son>
data () {
	return {
		data: '数据'
	}
}

// 子组件中
export default {
	// data 这个名字必须和父组件传值时使用的属性名相同
	props: ['data']
	// prop 校验
	props: {
		// simple 写法
		data: Number,
		// advanced 写法
		data: {
			type: Number,
			required: true,
			default: 666,
			// 自定义校验逻辑
			validator (val) {
				...
				return true;
			}
		}
	}
}
```

##### 子传父

- 组合式

```javascript
// 父组件中
// <Son @get-message='getMessage'></Son>
const getMessage = (msg) => {
	console.log(msg)
}

// 子组件中
// <button @click='sendMsg'>发送消息</button>
// 1. 通过 defineEmits 编译器宏生成 emit 方法
const emit = defineEmits(['get-message'])
const sendMsg = () => {
	emit('get-message', 'hey daddy')
}
```

- 选项式

```Javascript
// 父组件中
// <Son @changeData='changeFn'></Son>
methods: {
	changeFn (arg) {
		// arg: 子组件触发 changeData 携带的参数
	}
}

// 子组件中
// <button @click='handler'>...</button>
methods: {
	handler () {
		this.$emit('changeData', '666')
	}
}
```

##### 模板引用

> 通过 ref 标识获取 DOM 对象或者组件实例对象

- querySelector 是全局查找
- ref 和 $refs 的查找范围是当前组件

- 组合式

```html
<script>
// 1. 创建 ref 对象
const ref_1 = ref(null)
const ref_2 = ref(null)

// 页面渲染完成后聚焦到输入框上
onMounted(() => {
	ref_1.value.focus()
})
// 调用组件的方法
// setup 语法糖下，组件内部的属性和方法是不暴露给父组件的
// 可以在子组件通过 defineExpose({xxx}) 来暴露 xxx 方法，或其他属性
ref_2.xxx()

</script>

<template>
	<!-- 2. 通过 ref 标识绑定 ref 对象 -->
	<input ref="ref_1" type="text">
	<ComponentX ref="ref_2"></ComponentX>
</template>
```

- 选项式

```HTML
// 获取 DOM
// 1. 添加 ref 属性
<div ref='xxx'></div>
// 2. 通过 this.$refs.xxx 获取 DOM 结点（在 mounted 之后）

// 获取组件实例
// 1. 添加 ref 属性
<Component0 ref='xxx'></Component0>
// 2. 通过 this.$refs.xxx 获取目标组件（在 mounted 之后）
// 3. 通过 this.$refs.xxx.组件方法() 调用组件方法
```

##### 提供与注入

- 组合式

```javascript
// 顶层组件中
let val = ref(1)
provide('key', val)
// 底层组件不能修改这份数据，但是顶层组件可以提供修改这份数据的接口
provide('change_key', (new_val) => {
	val.value = new_val
})

// 底层组件中
const xxx = inject('key')
change_key(100)
```

- 选项式

```Javascript
// 父组件中
export default {
	provide() {
		return {
			// 传递简单类型时，子组件收到的不是响应式数据
			// 传递复合类型时，子组件收到的是响应式数据
			// 可以将简单类型包装成复合类型以实现响应式
			userInfo: ...
		}
	}
}

// 子组件中
export default {
	inject: ['userInfo']
}
```

##### 事件总线

```Javascript
// 1. 在 @/utils/eventBus.js 中创建一个事件总线
import Vue from 'vue'
const Bus = new Vue()
export default Bus

// 2. 发送方组件
import Bus from '@/utils/eventBus.js'
Bus.$emit('事件名', args)

// 3. 接收方组件
import Bus from '@/utils/eventBus.js'
create() { // 可以在该时机订阅
	Bus.$on('事件名', (args) => {
		...
	})
}
```

#### 编译器宏

##### defineOptions

> 在 vue3 使用了 setup 语法糖后，无法配置和 setup() 同级的配置项，vue3.3 后，可以使用 defineOptions 来定义这些配置项

```html
<script>
defineOptions({
	name: 'Foo'
})
</script>
```

##### defineModel

> 是为了简化 v-model 双向绑定而出现的

```javascript

```

#### 插槽

- 匿名插槽（默认插槽）

```html
<!-- 当组件内有一种不确定结构可以使用 -->

<!-- 组件 A -->
<B>code</B>

<!-- 组件 B -->
<div>
	<slot>default code</slot>
</div>
```

- 具名插槽

```html
<!-- 当组件内有多种不确定结构可以使用 -->

<!-- 组件 A -->
<B>
	<!-- 'v-slot:' 可以简写成 '#' -->
	<template v-slot:slot1>code</template>
	<template #slot2>code</template>
	<!-- 如果 B 中还有匿名插槽，使用 #default -->
</B>

<!-- 组件 B -->
<div>
	<slot name='slot1'>default code</slot>
	<slot name='slot2'>default code</slot>
</div>
```

- 作用域插槽

```html
<!-- 定义插槽的同时可以传值 -->

<!-- 组件 A -->
<B>
	<!-- 通过 #插槽名='形参' 接收 -->
	<template #default="test">code</template>
</B>

<!-- 组件 B -->
<!-- 以添加属性的方式传值 -->
<div>
	<slot :data="hello">default code</slot>
</div>
```

#### setup 与 setup 语法糖

```html
// 加上 setup 以允许直接在 script 中编写组合式 api
<script setup>
	const a = 1
</script>

// 相当于
<script>
	export default {
		setup() {
			const a = 1
			return {
				a
			}
		}
	}
</script>
```

- setup() 的执行时机比 beforeCreate 还早，其上下文中 this 是 undefined

#### $nextTick

```Javascript
// 场景: DOM 元素从隐藏变为显示后，无法立刻获得焦点，因为这一系列操作是异步的

this.isShow = true
this.$nextTick(() => {
	// 放在 $nextTick 后，能让 DOM 刷新后再聚焦
	this.$refs.XXX.focus()
})
```

#### SPA

|       单页应用       |       多页应用       |
| :--------------: | :--------------: |
| 所有功能在一个 HTML 上实现 | 应用在多个 HTMl 页面上实现 |
|     按需跟新，性能低     |     整页更新，性能低     |
|      开发效率高       |      开发效率低       |
|      用户体验好       |      用户体验差       |
|   学习成本高，需要学习路由   |      学习成本中等      |
|      首屏加载慢       |      首屏加载快       |
|     SEO 不友好      |      SEO 友好      |
|  用于文档类站点、移动端站点等  |    用于官网、电商类等     |
