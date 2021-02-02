---
title: js常见排序算法
tags: 
 - 算法 
 - js
---


## js常见排序算法

### 1. 冒泡排序
    
```javascript
//两次for循环, 第一次从后往前遍历, 第二次遍历到一个位置
function bubbleSort(arr) {
  for (let i = arr.length - 1; i >= 0; i--) {
    for (var j = 0; j < i; j++) {
      if (arr[i] > arr[j]) {
        [arr[i], arr[j]] = [arr[j], arr[i]]
      }
    }
  }
  return arr
}
```

### 2. 快速排序

```javascript
// 选择一个值作为中点, 大于值的往右,小于的往左
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr
  }
  let flag = arr.splice(Math.floor(arr.length / 2),1)[0]
  let left = [];
  let right = []
  for (var i = 0; i < arr.length; i++) {
    if (arr[i] < flag) {
      left.push(arr[i])
    } else {
      right.push(arr[i])
    }
  }
  return quickSort(left).concat([flag], quickSort(right))
}
```


### 3. 归并排序

```javascript
// 数组一分为二, 从最小粒度开始,左右数组对比,依次插入新的数组返回 
function mergeSort(arr) {
  if (arr.length <= 1) {
    return arr
  }
  let flag = Math.floor(arr.length / 2)
  let left = arr.slice(0, flag);
  let right = arr.slice(flag)
  return merge(mergeSort(left), mergeSort(right))
}

function merge(left, right) {
  let arr = []
  while(left.length && right.length) {
    if (left[0] < right[0]) {
      arr.push(left.shift())
    } else {
      arr.push(right.shift())
    }
  }
  return arr.concat(left, right)

}
```


### 4.插入排序

```javascript 
//类似摸扑克牌,每次拿一个数,从尾到头查找
function insertSort(arr) { 
  if (arr.length <= 1) {
    return arr
  }
  for (let i = 1; i < arr.length; i++) {
    let current = arr[i];
    j = i - 1
    while(j > 0 && arr[j] > current) {
      arr[j + 1] = arr[j];
      j--
    }
    arr[j + 1] = current
  }
  return arr
}
```
	
### 排序对比

|  排序算法 | 最好时间复杂度  | 最坏时间复杂度 | 平均时间复杂度 | 空间复杂度 | 是否稳定  | 
|  ------- | ------------ | -------------| ------------ | -------  | ------  | 
|  冒泡排序 | O(n) |  O(n^2) | O(n^2)  | O(1)  | 稳定 |
|  快速排序 | O(nlogn) |  O(n^2) | O(nlogn)  | O(1)最好：O(logn); 最坏： O(n)  | 不稳定 |
|  归并排序 | O(nlogn) |  O(nlogn) | O(nlogn)  | O(n)  | 稳定 |
|  插入排序 | O(n) |  O(n^2) | O(n^2)  | O(1)  | 稳定 |




