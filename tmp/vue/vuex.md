### 安装

```Javascript
// 1. 安装 vuex3
// yarn add vuex@3

// 2. 在 @store/index.js 中存放 vuex

// 3.1 在 store/index.js 中，导入 Vuex 模块
import Vue from 'vue'
import Vuex from 'vuex'
// 3.2 插件安装
Vue.use(Vuex)
// 3.3 创建仓库
const store = new Vuex.Store()

// 4.1 导出给 main.js 使用
export default store
// 4.2 在 main.js 中，将 store 挂载到 Vue 实例中
import store from '@sotre/index.js'
new Vue({
	render: h => h(App),
	store
}).$mount('#app')
// 4.3 可以使用 this.$store 访问到仓库
```

### state

```Javascript
// 所有共享的数据都要放到 state 中存储
const store = new Vuex.Store({
	state: {
		data: 1
	}
})
// 模板中可以通过 this.$store.state.变量名 获取数据
// 模块中可以通过 store.state.变量名 获取数据
// 可以把 state 中的数据定义再计算属性中
computed: {
	变量名 () {
		return this.$router.state.变量名
	}
}
// Vuex 提供了 mapState，可以自动将 store 中的数据映射到组件的计算属性中
import { mapState } from 'vuex'
export default {
	computed: {
		...mapState(['变量名1', '变量名2']),
		其他计算属性
	}
}
```

### mutations

```Javascript
// Vuex 同样遵循单向数据流，组件中不能直接修改仓库中的数据
// 如果直接修改了，为了性能，默认不会报错，可以开启报错功能
const store = new Vuex.Store({
	strict: true, // 上线时需要移除
	...
})

// mutations 是 Store 的一个对象，用来存放修改 state 的方法
const store = new Vuex.Store({
	mutations: {
		addXXX(state, arg) {
			state.XXX += arg
		}
	}
})

// 修改仓库的数据，需要向仓库 commit
this.$store.commit('mutations 中的方法名', arg)
// 如果要传递多个参数
this.$store.commit('mutations 中的方法名', {
	K1: V1,
	K2: V2
}) // 对应的方法也只能有两个参数，不过第二个形参是一个对象

// 与 mapState 类似，mapMutations 将位于 mutations 中的方法映射到 methods中
improt { mapMutations } from 'vuex'
methods: {
	...mapMutations(['方法名']), // 使用 this.方法名调用
	其他方法
}
```

### actions

```Javascript
// action 是用来处理异步操作的，前面的 mutations 必须是同步的

// 1. 在 store 中提供 actions
const store = new Vuex.Store({
	actions: {
		方法名 (context, arg) {
			// 这里用 setTimeout 模拟异步
			setTimeout(() => {
				context.commit('一个 mutations 函数', arg)
			}, 1000)
		}
	}
})

// 2. 调用异步逻辑
this.store.$dispatch('action_name', arg)

// 使用 mapActions 把位于 actions 中的方法映射到组件的 methods 中
import { mapActions }  from 'vuex'
methods: {
	...mapActions(['action 名']), 
	其他方法
}
```

### getters

```Javascript
// 类似于计算属性

// 定义 getters
const store = new Vuex.Store({
	getters: {
		// 第一个形参是 state
		XXX(state) {
			return state.xxx * 2
		}
	}
})

// 2.1 通过 store 访问 getters
this.$store.getters.XXX

// 2.2 通过 mapGetters 映射 getters
import {{ mapGetters }} from 'vuex'
computed: {
	...mapGetters(['XXX']),
	其他计算属性
}
```

### 模块

```Javascript
// 每一个模块都有自己的 states、actions、mutations、getters

// 新建一个模块，假设名为 user
// 建立路径 @/store/modules/user.js，在 user.js 
const state = {...}
const mutations = {...}
const actions = {...}
const getters = {...}
export default {
	state,
	mutations,
	actions,
	getters
}
// 在 @/store/index.js 中
import user from './modules/user.js'
const store = new Vuex.Store({
	modules: {
		user
	}
})
```

### 访问模块中的数据

#### state

> 尽管已经分模块，子模块的状态还是会挂到根级别的 `state` 中

```Javascript
// 方式 1.
$store.state.模块名.XXX

// 方式 2. 通过 mapState 映射
// 2.1. 在模块默认导出中添加配置项 namespaced: true
// 2.2. 映射模块
mapState(['模块名'])
// 2.3. 如果只映射模块中的部分数据（需要开启命名空间）
mapState('user', ['userInfo', 'userDebt'])
```

#### getters

```Javascript
// 方式 1.
$store.getters['模块名/xxx']

// 方式 2. 映射某个模块中的一些数据
mapGetters(['user'])
mapGetters('user', ['user模块中的数据']) // 需要开启命名空间
```

#### mutations

> 子模块中的 mutations 和 actions 会被默认挂载到全局，需要开启命名空间，才会挂载到子模块

```Javascript
// 调用子模块中的 mutations

// 方式 1
$store.commit('模块名/mutation_name', args)

// 方式 2
methods: {
	...mapMutations('模块名', ['mutation1', 'mutation2'...])
}
```

#### actions

> actions 同 mutations 也是类似
