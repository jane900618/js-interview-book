# 笔记 - ES6

## let 和 const
* 不可以重复声明
* 块级作用域
* 不存在变量提升 ==> 声明变量前使用会出报错ReferenceError（现暂时性死区）

## 解构赋值
```
// 注意点：
let [a, ...b] = [1, 2, 3, 4]; // a:1 b: [2,3,4] 注意下b=[2,3,4]
let [a, [b], d] = [1, [2, 3], 4]; // a: 1 b:2 c:4 注意下b=[2]
let [x = 1] = [undefined]; // x = 1
let [x = 1] = [null]; // x = null
var {x = 3} = {x: undefined}; // x = 3
var {x = 3} = {x: null}; // x = null
let {0 : first, [arr.length - 1] : last} = [1, 2, 3]; // first:1 last:3
```

## 箭头函数
* `this`指向函数所在上下文
* 不可以用call、apply、bind改变this指向
* 没有自己的arguments，可以访问外围函数的arguments
* 不能通过`new`实例化
* 没有原型

## Symbol: 独一无二的值
定义：
```
let s = Symbol();
let s1 = Symbol('foo');
let sym = Symbol(obj);
```
* 可作为属性值
* 可作为常量

## Prromise

## async/await

