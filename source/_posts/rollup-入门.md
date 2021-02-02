---
title: Rollup 入门
date: 2020-01-17 14:29:36
tags: rollup
---

### 介绍
> Rollup 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码，例如 library 或应用程序。相比webpack, 生成的代码更加简洁，干净，代码可读性相对也更加良好。
### 

### 优势
1. 编译出来的代码`可读性好`
2. Rollup打包后生成的bundle内容十分`干净`，没有什么多余的代码，只是将各个模块按照依赖顺序拼接起来，所有模块构建在一个函数内（Scope Hoisting）, 执行效率更高。相比webpack(webpack打包后会生成__webpack_require__等runtime代码)，Rollup拥有无可比拟的性能优势，这是由依赖处理方式决定的，`编译时依赖处理（rollup）自然比运行时依赖处理（webpack）性能更好`
3. 对于ES模块依赖库，Rollup会静态分析代码中的 import，并将排除任何未实际使用的代码(tree-shaking，下文会有具体解释)
4. 支持程序流分析，能更加正确的判断项目本身的代码是否有副作用(配合tree-shaking)
5. 支持导出`es`模块文件（webpack不支持导出es模块）


### 打包第一个js

1. 创建目录rollup_demo并用npm初始化`package.json`
2. 使用npm全局安装Rollup ，安装Rollup
	`npm install rollup -g` 
3.  创建main.js
 
	```
	export default function helloWorld () {
		console.log('hello world~')
	}
	```

4.  创建roll.config.js文件，使用以下配置
	
	```
	export default {
	  input: 'main.js', // 入口文件
	  output: {
	    file: 'bundle.js',  // 打包的文件名
	    format: 'cjs'  // 生成包的方式
	  }
	}; 
	```
5. 控制台运行`rollup -c`命令，打包main.js
6. rollup在当前目录中生成`bundle.js` 

### rollup常见属性
#### 1. input(-i/--input)
>输入路径， 打包的入口相当于webpack entry

#### 2. output.file(-o/--output.file)
>打包的输出的文件名

#### 3. output.format(-f/--output.format)
>**生成的包的格式**
>
* amd – 异步模块定义，用于像RequireJS这样的模块加载器
* cjs – CommonJS，适用于 Node 和 Browserify/Webpack
* esm – 将软件包保存为 ES 模块文件，在现代浏览器中可以通过 `<script type=module>` 标签引入
* iife – 一个自动执行的功能，适合作为`<script>`标签。（如果要为应用程序创建一个捆绑包，您可能想要使用它，因为它会使文件大小变小。）
* umd – 通用模块定义，以`amd`，`cjs` 和 `iife` 为一体
* system - SystemJS 加载器格式

#### 4. output.name(-n/--name)
>包的变量名
>
> ```// rollup.config.js
export default {
  ...,
  output: {
    file: 'bundle.js',
    format: 'iife',
    name: 'MyBundle'
  }
};
// -> var MyBundle = (function () {...
```

#### 5. plugin
>插件

#### 6. external
>外部扩展
>
>```// rollup.config.js
import path from 'path';
export default {
  ...,
  external: [
    'some-externally-required-library',
    path.resolve( './src/some-local-file-that-should-not-be-bundled.js' )
  ]
};
```

### 常用的Rollup插件
 ....
