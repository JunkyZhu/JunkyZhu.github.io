---
title: Sentry
tags: Sentry
---

## Sentry 

### 背景
  线上日志监控：  保证项目质量，及时跟踪问题，复现客户场景
  
*   	业务监控，百度统计、GA
*   	性能监控，window.performance
*   	错误监控  

  
### 介绍
	实时事件日志记录和聚合平台，用于监视错误和提取执行适当的事后操作所需的所有信息

### 使用
     
####  1. vue

* 使用yarn安装依赖

		yarn add @sentry/browser @sentry/integrations -S
		
* `main.js`中引入


	```javascript
	import Vue from 'vue'
	import * as Sentry from '@sentry/browser';
	import { Vue as VueIntegration } from '@sentry/integrations';
		
	Sentry.init({
		dsn: 'http://{key}@sentry.skytech.cn/1',
		 integrations: [new VueIntegration({Vue, attachProps: true})],
	});
	```
	
### sourcemap上传

#### 1. 通过sentry-cli上传

#### 2. 通过webpack上传


	 