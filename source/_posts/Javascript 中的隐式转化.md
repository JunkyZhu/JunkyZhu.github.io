---
title: Javascript 中的隐式转化
date: 2023-04-23 18:46:58
tags: Javascript
---


* ### JavaScript中的'=='的比较规则

1. 如果两个操作数的类型相同，则直接比较它们的值。

2. 如果两个操作数的类型不同，则将它们转换为相同的类型再进行比较。

3. 如果一个操作数是null，另一个操作数是undefined，则它们相等。

4. 如果一个操作数是数字，另一个操作数是字符串，则将字符串转换为数字再进行比较。

5. 如果一个操作数是布尔值，另一个操作数是非布尔值，则将布尔值转换为数字再进行比较。

6. 如果一个操作数是对象，另一个操作数是数字或字符串，则将对象转换为原始值再进行比较。

> 需要注意的是，使用“==”运算符时需要小心，因为它的隐式类型转换可能会导致意想不到的结果。建议使用“===”运算符进行严格相等比较。

* ### Javascript中的'<' 或 '>' 比较规则


1. 如果比较的两个值都是数字类型，则直接比较它们的大小关系。

2. 如果比较的两个值中有一个是字符串类型，则将另一个值转换为字符串类型，然后按照字典序比较它们的大小关系。

3. 如果比较的两个值中有一个是布尔类型，则将布尔值转换为数字类型（true转换为1，false转换为0），然后按照数字大小比较它们的大小关系。

4. 如果比较的两个值中有一个是对象类型，则将对象转换为原始值，然后按照上述规则比较它们的大小关系。

5. 如果比较的两个值中有一个是null或undefined，则将其转换为数字类型，null转换为0，undefined转换为NaN，然后按照数字大小比较它们的大小关系。



* ### Javascript中对象相关的隐式转化逻

1. 在JavaScript中，将对象转换为原始值的过程称为“类型转换”或“强制类型转换”。对象转换为原始值的规则如下：

2. 如果对象具有valueOf()方法并且返回原始值，则将其返回值作为对象的原始值。

3. 否则，如果对象具有toString()方法并且返回原始值，则将其返回值作为对象的原始值。

4. 否则，如果对象是数组，则将其转换为字符串，即返回由数组元素组成的逗号分隔的字符串。

5. 否则，返回对象本身。

对于空数组[]，其原始值是空字符串""。如果你想将一个对象转换为原始值，可以使用valueOf()和toString()方法来控制转换的结果。例如，可以重写数组的valueOf()方法来返回一个特定的原始值，如下所示：

```
var arr = [];
arr.valueOf = function() {
  return 42;
};
console.log(arr == 42); // true
```
在这个例子中，将空数组转换为原始值时，会调用valueOf()方法并返回42，因此表达式arr == 42的结果为true。

对于空对象，空对象的原始值是`"[object Object]"`，它是一个字符串类型的值。当将一个空对象转换为原始值时，JavaScript会默认调用该对象的toString()方法，该方法返回一个字符串，其中包含对象的类型信息和内存地址。由于空对象没有自定义的toString()方法，因此会调用Object.prototype.toString()方法，该方法返回"[object Object]"字符串。因此，空对象的原始值就是`"[object Object]"`。

