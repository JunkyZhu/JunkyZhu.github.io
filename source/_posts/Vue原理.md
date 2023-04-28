---
title: Vue原理
date: 2020-11-16 10:11:58
tags: Vue.js
---

### 1. new Vue()的时候干了什么;
 
 1. 初始化声明周期
 2. 初始化事件
 3. 初始化渲染方法
 4. 调用beforeCreated钩子
 5. 处理injections 参数
 6. 初始化State(data、props、computed、watch )
 7. 初始provide
 8. 调用created 钩子
 9. 如果有el参数直接调用$mount挂载dom

### 2. Vue响应式原理

``` html
  	<div id="app">
	 	<p>{{msg}}</p>
	 	<span>{{content}}</span>
	 	<div v-text="msg"></div>
	 	<input type="text" v-model="msg" />
	</div>
	
```
```javascript

	new Vue({
		el: 'app'
		data: {
			number: 0
		},
		render() {
			document.getElementById('myApp').text = data.number
		}
	})
	class Observer {
		constructor(data) {
			this.walker(data)
		}
		walker(data) {
			Object.keys(data).forEach(key => {
				this.defineReactive(data, key, data[key])
			})
		}
		defineReactive(data, key, value) {
			let _value = value
			Object.defineProperty(data, key, {
				enumerable: true,
				configurable: true,
				get() {
					return _value
				}
				set(newVal) {
					if(newVal === _value) return
					_value = newVal
					addDep()
				}
			})
		
		} 
	}
	class Vue {
		constructor(options) {
			// 1. 通过属性保存选项数据
			this.$options = options || {}
			this.$data = options.data || {}
			this.$el = typeof options.el === 'string' ? document.querySelector(options.el) || el
			// 2.data数据保存到vue实例getter setter里面
			this._proxyData(this.$data)
			// 3. 调用observe 方法，监听数据变化
			// 4. 调用compile 解析指令和插值表达式
		}
		_proxyData(data) {
			Object.keys(data).forEach(key => {
				Object.defineProperty(data, key, {
					enumerable: true,
					configurable: true,
					get() {
						return data[key]
					},
					set(value) {
						if(value === data[key]) return
						data[key] = value
					}
				})
			})
		}
	}
	function Vue(options) {
		
		this._init(options)
		this.data = options.data
		
	}
	Vue.prototype.setVal = function(data) {
		this.data = data
	}
	Vue.prototype._init = function (options) {
		this.data = options.data
		reactive(options.data)
		new Watcher(() => {
			options.render.call(this)
		})
	}
	function reactive(data) {
		Object.keys(data).forEach(key => {
			defineReactive(data, key,)
		})
	}
	
	function defineReactive(data, key) {
		let value = data[key];
		let dep = new Dep();
		Object.defineProperty(data, key, {
			get() {
				dep.append()
				return value
			},
			set(newVal) {
				value = newValue
				dep.notify()
			}
		})
	} 
	
	class Dep {
		constructor() {
			this.deps = new Set()
		}
		append() {
			this.deps.add(Dep.target)
		}
		notify() {
			this.deps.forEach(watcher => watcher.update())
		}
	}
	Dep.target = null
	// watcher栈
	const targetStack = []
	
	// 将上一个watcher推到栈里，更新Dep.target为传入的_target变量。
	function pushTarget(_target) {
	  if (Dep.target) targetStack.push(Dep.target)
	  Dep.target = _target
	}
	
	// 取回上一个watcher作为Dep.target，并且栈里要弹出上一个watcher。
	function popTarget() {
	  Dep.target = targetStack.pop()
	}
	
	class Watcher {
		constructor(getter) {
			this.getter = getter
			this.get()
		}
		get() {
			pushTarget(this)
			this.value = this.getter()
			popTarget()
			return this.value
		}
		update() {
			this.get()
		}
	}
	
	
```


### 3. vue为什么组件的data需要是function？
	vue组件可能会被多处复用，实际是对组件的对象的引用，实例化发生在组件加载时，用对象会出现引用同一个内存地址的情况

### 4. vue为什么组件的computed是惰性求值，当且仅当相关的state发生变化才会调用？
	watcher 内部会用value属性缓存当前计算属性的值，vue每次更新会去查看依赖的data是否更新(dirty 是否为true)，没有更新直接取value，更新了重新计算

### 5. vue为什么使用vdom， vdom是如何更新的？
虚拟dom的好处： 可跟踪状态，创建开销小；适合较复杂的场景



```
// 发布订阅模式（vue事件机制）
class EventBus {
    state = []
    $emit(name, ...args) {
        this.state[name].forEach(fn => fn(...args))
        
    }
    $on(name, fn) {
        if(!this.state[name]) {
            this.state[name] = [fn] 
        }else {
            this.state[name].push(fn)
        }
        
    }
}

// 观察者模式 （vue 响应式）

class Dep {
	constructor() {
		this.subs = []
	}
	addSub(vm) {
		this.subs.push(vm)
	}
	notify() {
		this.subs.forEach(vm => vm.update())
	}
}
class Watcher {
 	update() {
 		
 	}

}
```

### vue beforeCreated -> created 阶段

1.  initInject；如果有inject 属性，对inject属性进行响应式监测
2. initState；处理props、data、methods、computed、watch
3. initProvide；

	
	

[vue技术内幕](http://caibaojian.com/vue-design/art/)