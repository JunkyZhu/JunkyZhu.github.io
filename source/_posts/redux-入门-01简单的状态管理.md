---
title: redux 入门
date: 2019-12-28 11:15:58
tags: redux
---

## redux

```javascript
function createStore(initState) {
	let state = {}
	let listeners = []
	let subscribe = function(listener) {
		listeners.push(listener)
	}

	let changeState = function(newState) {
		state = newState
		for (var i = 0; i < listeners.length; i++) {
			listeners[i]()
		}
	}
	let getState = function() {
		return state
	}
	return {
		subscribe,
		changeState,
		getState
	}
}

// let initState = {
// 	count: 0,
// 	info: {
// 		name: 'a',
// 		gender: 'male'
// 	}
// }
// let {getState, changeState, subscribe} = createStore(initState)
// subscribe(function() {
// 	console.log('监听到了')
// 	console.log(getState())
// })
// changeState({
// 	...getState(),
// 	count: 1
// })
// changeState({
// 	...getState(),
// 	info: {
// 		name: 'b',
// 		gender: 'female'
// 	}
// })


function plan(state, action) {
	switch(action.type) {
		case 'INCREMENT':
			return {
				...state,
				count: state.count + 1
			}
		case: 'DECREMENT':
			return {
				...state,
				count: state.count + 1
			}
		default: 
			return state
	}
		
}


function createStore(plan, initState) { //有计划
	let state = {}
	let listeners = []
	let subscribe = function(listener) {
		listeners.push(listener)
	}

	let changeState = function(action) {
		state = plan(state, action)
		for (var i = 0; i < listeners.length; i++) {
			listeners[i]()
		}
	}
	let getState = function() {
		return state
	}
	return {
		subscribe,
		changeState,
		getState
	}
}

/****change name*****/
function countReducer(state, action) {
	switch(action.type) {
		case 'INCREMENT':
			return {
				...state,
				count: state.count + 1
			}
		case: 'DECREMENT':
			return {
				...state,
				count: state.count + 1
			}
		default: 
			return state
	}
		
}


function createStore(reducer, initState) { //有计划
	let state = {}
	let listeners = []
	let subscribe = function(listener) {
		listeners.push(listener)
	}

	let dispatch = function(action) {
		state = reducer(state, action)
		for (var i = 0; i < listeners.length; i++) {
			listeners[i]()
		}
	}
	let getState = function() {
		return state
	}
	return {
		subscribe,
		dispatch,
		getState
	}
}




/******多状态协作*****/

function countReducer(state = 0, action) {
	switch(action.type) {
		case 'INCREMENT':
			return {
				...state,
				count: state.count + 1
			}
		case 'DECREMENT':
			return {
				...state,
				count: state.count + 1
			}
		default: 
			return state
	}
		
}

function infoReducer(state = {name: '123', gender: 'ab'}, action) {
	switch(action.type) {
		case 'CHANGENAME':
			return {
				...state,
				name: action.name
			}
		case 'CHANGEGENDER':
			return {
				...state,
				gender: action.gender
			}
		default: 
			return state
	}
		
}

function combineReducer(reducers) { //合并reducers
	const reducerKeys = Object.keys(reducers)
	
	return function(state = {}, action) {
		let nextState = {}
		for (var i = 0; i < reducerKeys.length; i++) {
			const key = reducerKeys[i]
			const reducer = reducers[key]
			const previousState = state[key]
			const nextKeyState = reducer(previousState, action)
			nextState[key] = nextKeyState
		}
		return nextState
	}
}

const reducer = combineReducer({
	count: countReducer,
	info: infoReducer
})


function createStore(reducer, initState) { //有计划
	let state = initState
	let listeners = []
	let subscribe = function(listener) {
		listeners.push(listener)
	}

	let dispatch = function(action) {
		state = reducer(state, action)
		for (var i = 0; i < listeners.length; i++) {
			listeners[i]()
		}
	}
	let getState = function() {
		return state
	}
	dispatch({type: Symbol()})
	return {
		subscribe,
		dispatch,
		getState
	}
}
const store = createStore(reducer)
const next = store.dispatch

store.dispatch = (action) => {
	console.log('------ before state -------')
	console.log(store.getState())
	console.log('------ action -------')
	console.log(action)
	next(action)
	console.log('------ after state -------')
	console.log(store.getState())
}

let loggerMiddleware = store => next => action => {
	console.log('------ before state -------')
	console.log(store.getState())
	console.log('------ action -------')
	next(action)
	console.log('------ after state -------')
	console.log(store.getState())
}
let expectionMiddleware = store => next => action => {
	try {
		next(action)
	} catch(e) {
		console.log(e)
	}
}
const logger = loggerMiddleware(store);
const expection = expectionMiddleware(store);

store.dispatch = expection(logger(next))



function createStore(reducer, initState, rewriteStore) { //有计划
	let state = initState
	let listeners = []
	if (rewriteStore) {
		const newCreateStore = rewriteStore(createStore);
		return newCreateStore(reducer, initState)
	}
	let subscribe = function(listener) {
		listeners.push(listener)
	}

	let dispatch = function(action) {
		state = reducer(state, action)
		for (var i = 0; i < listeners.length; i++) {
			listeners[i]()
		}
	}
	let getState = function() {
		return state
	}
	dispatch({type: Symbol()})
	return {
		subscribe,
		dispatch,
		getState
	}
}
const applyMiddleware = function(...middlewares) {
	return createStore => (reducer, initState) => {
		const store = createStore(reducer, initState)
		let dispatch = store.dispatch
		let chain = middlewares.map(middleware => middleware(store))
		chain.reverse().map(middleware => {
			dispatch = middleware(dispatch)
		}) 
		store.dispatch = dispatch
		return store
	}
}
const store = createStore(reducer, null, applyMiddleware(loggerMiddleware, expectionMiddleware))

```