---
title: 手写promise
date: 2020-12-02 16:40:56
tags: 
   - javascript
   - promise
---

### 模拟实现promise

```

const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';


function resolvePromise(p, result, resolve, reject) {
	if (p === result) {
		throw new Error('value can\'t be self')
	}
	if ((typeof result === 'object' && result !==null )|| typeof result === 'function') {
		let then = result.then
		if (typeof then === 'function') {
			let called = false;
			try {
				then.call(result, r => {
					if (called) return
					called = true

					resolvePromise(p, r, resolve, reject)
				}, reason => {
					if (called) return
					called = true
					reject(reason)
				})
			} catch(e) {
				reject(e)
			}
			
		} else {
			resolve(result)
		}
	} else {
		resolve(result)
		
		
	}
}

/*
* Promise 有三个状态, 状态变更为fulfilled 或者reject那则不会继续变更;
* Promise 变更状态后 reason 或者 result值会在then返回	
* 1. 链式调用.then -> .then ->
* 2. then 方法需要加入到下一个消息队列执行(setTimeout模拟)
* 3. 支持延迟结果(异步)
*/
class Promise {
	constructor(executor) {
		this.state = PENDING;
		this.result = null;
		this.reason = null;
		this.resolveCallbackQueue = [];
		this.rejectCallbackQueue = [];
		let resolve = (val) => {
			if (val instanceof Promise) {
				return val.then(resolve, reject)
			}
			if (this.state === PENDING) {
				this.state = FULFILLED;
				this.result = val;
				this.resolveCallbackQueue.forEach(callback => callback(this.result))
			}
		}
		let reject = (reason) => {
			if (this.state === PENDING) {
				this.state = REJECTED;
				this.reason = reason;
				this.rejectCallbackQueue.forEach(callback => callback(this.reason))
			}
		}
		try {
			executor(resolve, reject)
		} catch(e) {
			reject(e)
		}
		
	}
	static resolve(val) {
		return new Promise((resolve, reject) => {
			resolve(val)
		})
	}
	static reject(reason) {
		return new Promise((resolve, reject) => {
			reject(reason)
		})
	}
	then(onFulfilled, onRejected) {
		// 调用then的时候,如果state是pengding状态,说明是异步结果,通过发布订阅处理结果
		onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;
		onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err };
		let p = new Promise((resolve, reject) => {
			if (this.state === PENDING) {
				this.resolveCallbackQueue.push(() => {
					setTimeout(() => {
						try {
							let result = onFulfilled(this.result)
							resolvePromise(p, result, resolve, reject)
						} catch(e) {
							reject(e)
						}
					}, 0)
				})
				this.rejectCallbackQueue.push(() => {
					setTimeout(() => {
						try {
							let reason = onRejected(this.reason)
							resolvePromise(p, reason, resolve, reject)
						} catch(e) {
							reject(this.reason)
						}
					}, 0)
				})
			}
			if(this.state === FULFILLED) {
				setTimeout(() => {
					try {
						let result = onFulfilled(this.result)
						resolvePromise(p, result, resolve, reject)
					} catch(e) {
						reject(e)
					}
				}, 0)
			}
			if (this.state === REJECTED) {
				setTimeout(() => {
					try {
						let reason = onRejected(this.reason)
						resolvePromise(p, reason, resolve, reject)
					} catch(e) {
						reject(this.reason)
					}
				}, 0)
			}
		})
		return p
		
	}
	catch(callback) {
		return this.then(null, callback)
	}
}

// 示例
function asyncFn() {
	return new Promise((resolve, reject) => {
		console.log('promise start')
		setTimeout(() => {
			console.log('promise settimeout 2000')
			resolve(10)
		}, 2000)
	} )
}
console.log('start')
setTimeout(function() {
	console.log('setTimeout 2000')
}, 2000)

asyncFn().then(val => {
	console.log(111, val)
	return val
}).then(val => {
	console.log(222, val)
}).then(val => {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			resolve(30)
		}, 3000)
	} )
}).then(val => {
    console.log(333, val)
})
console.log('end')

```