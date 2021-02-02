---
title: JS 常见面试题
date: 2020-12-02 16:40:56
tags:
---


### 1. 模拟实现js new操作符

[new 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)

```javascript
	function newOperator(ctor, ...args) {
		if(typeof ctor !=='function') {
			throw new Error('first param must be function!')
		}
		newOperator.target = ctor
		
		// 以构造函数原型对象为基础创建一个对象
		let obj = Object.create(ctor.prototype);
		
		// 调用构造函数方法，如果返回值是对象或者方法return 返回值；否则返回新的对象
		let result = ctor.apply(obj, args);
		const isObj = typeof result === 'object' && result !== null
		const isFn = typeof result === 'function'
		if (isFn || isObj) {
			return result
		} else {
			return obj
		}
	}

```


### 2. ES5 和 ES6继承区别

1、原型链继承

> 优点： 原型方法可以共享
> 
> 缺点： 继承时不能传参， 原型中包含引用值的时候会在所有实例共享
	
```
	function Parent(name) {
	    this.list = ['1', '2'];
	    this.name = name;
	    this.str = new String('123')
	    this.getName = function() {
	        return this.name
	    }
	}
	Parent.prototype.getStr = function () {
	    return this.str
	}
	function Children() {}
	Children.prototype = new Parent()
	child1 = new Children('child1')
	child2 = new Children('child2')
	child1.list === child2.list // true
	child1.str === child2.str // true
	child1.list.push('3')
	console.log(child1, child2) //['1', '2', '3'], ['1', '2', '3']
	// 继承SuperType
	SubType.prototype = new SuperType();
	SubType.prototype.getSubValue = function () {
	  return this.subproperty;
	};
	let instance = new SubType();
	console.log(instance.getSuperValue()); // true
```
	
	
2、 盗用构造函数继承

> 	优点： 子类能向父类构造函数传参； 原型上引用类型不会共享
> 
>    缺点：函数不能复用；子类不能访问父类原型上定义的方法
	
```
	function Parent(name) {
	    this.list = ['1', '2'];
	    this.name = name;
	    this.str = new String('123')
	    this.getName = function() {
	        return this.name
	    }
	}
	Parent.prototype.getStr = function () {
	    return this.str
	}
	function Children(name) {
        Parent.call(this, name)
    }
	child1 = new Children('child1')
	child2 = new Children('child2')
	child1.list === child2.list // false
	child1.str === child2.str // true
	child1.getName === child2.getName // false;
	child.getStr //undefined
	
```

	
3、组合继承（结合原型链和盗用构造函数的方式）

> 	优点：方法可以定义在原型上复用，实例又有自己的属性
> 
>    缺点：父类被调用两次

	
```
	function Parent(name) {
	    this.list = ['1', '2'];
	    this.name = name;
	    this.str = new String('123')
	    this.getName = function() {
	        return this.name
	    }
	}
	Parent.prototype.getStr = function () {
	    return this.str
	}
	function Children(name) {
        Parent.call(this, name)
    }
    Children.prototype = new Parent()
	//Children.prototype = new Parent()
	child1 = new Children('child1')
	child2 = new Children('child2')
	child1.list === child2.list // false
	child1.getStr === child2.getStr // true
```
	
4、原型式继承(Object.create)
	
> 	优点：不需要单独创建构造函数，对象间能共享信息；对传入的对象进行了浅拷贝
> 
>    缺点：引用类型会被共享，不能传递参数
	
```
	function createObj(ctor) {
		function F() {}
		F.prototype = ctor
		return new F()
	
	}
```

5、寄生式继承
	
> 	优点：通过调用函数创建一个对象
> 
>    缺点：与盗用构造函数类似，函数难以复用
	
```
	function createObj(ctor) {
		function F() {}
		F.prototype = ctor
		return new F()
	
	}
	function createAnother(original){
		let clone = createObj(original); // 通过调用函数创建一个新对象 clone.sayHi = function() { // 以某种方式增强这个对象
		        console.log("hi");
		      };
		return clone; // 返回这个对象
	 }
	 let person = {
	      name: "Nicholas",
	      friends: ["Shelby", "Court", "Van"]
	};
	    let anotherPerson = createAnother(person);
	    anotherPerson.sayHi();  // "hi"

	
```

6、寄生式组合继承
	
> 	优点：只调用了一次父级构造函数，原型链也保持不变
> 
>    缺点：不能继承父类的静态方法静态属性
	
```
	function Parent(name) {
	    this.list = ['1', '2'];
	    this.name = name;
	    this.str = new String('123')
	    this.getName = function() {
	        return this.name
	    }
	}

	Parent.prototype.getStr = function () {
	    return this.str
	}
   function inheritPrototype(subType, superType) {
		let prototype = Object.create(superType.prototype);
		prototype.constructor = subType;
		subType.prototype = prototype;
	
	}
	function Children(name) {
        Parent.call(this, name)
   }
   inheritPrototype(Children, Parent)
	child1 = new Children('child1')
	child2 = new Children('child2')
	
	
```

7、ES6 class extends 继承
	
> 	ES5的继承实质上是先创建子类的实例对象，然后再将父类的方法添加到this上
> 
>    ES6的继承有所不同，实质上是先创建父类的实例对象this，然后再用子类的构造函数修改this。因为子类没有自己的this对象，所以必须先调用父类的super()方法，否则新建实例报错。
> 	

```
	class Parent {
		constructor(name) {
			this.list = ['1', '2'];
			this.name = name;
			this.str = new String('123')
		}
		getName() {
			return this.name
		}
 	}
 	class Children extends Parent {
 		constructor(name) {
 			super(name)
 		}
 	}
	child1 = new Children('child1')
	child2 = new Children('child2')
	
	
```

### 3. call & apply & bind
