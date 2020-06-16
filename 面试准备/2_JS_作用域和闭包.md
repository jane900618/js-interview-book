# 面试准备2 - JS篇

## 1、原型/构造函数/实例
函数经过`new`生成的对象 ==> 实例 ==> 实例的`__proto__ ` 指向==> 构造函数的`prototype`
```
function F(){}
const f = new F()

f.__proto__ === F.prototype
f.constructor === F
F.prototype.constructor === F
F.__ptoto__ === Function.prototype
```

## 2、原型链
每个对象都有一个`__proto__ `可以查找父类原型对象，直到找到Object为止，这样形成一个原型指向的链条 ==> 原型链 ==> **实现继承和共享属性**

## 3、执行上下文
三个部分：
* 变量对象(VO)：存储当前执行上下文的**变量和函数声明**
* 作用域链：`[[scope]]`父级和自身的变量对象`AO` ==> 可以通过作用域链查找父级声明的变量和函数
* this指向

三种类型：
* 全局执行上下文
* 函数执行上下文
* eval执行上下文

代码执行过程：
1. 创建全局执行上下文
2. 遇到函数，函数执行上下文被push到执行顶栈
3. 执行函数
4. 函数执行完毕，被推出执行栈，继续执行

## 4、闭包
> 一个函数可以访问另一个函数中的变量

### 解决
* 利用匿名函数自执行
* 给定时器传参
* 使用ES6 `let`

## 5、new运算符
* 创建一个新对象：let obj = {}
* 改变原型指向：obj.__proto = Constructor.prototype
* 绑定this：let ret = Constructor.apply(obj, arguments)
* 返回新对象：return typeof ret === 'object' ? ret : obj

## 6、instanceOf 原理
> 基于原型链，只要处于原型链中，返回true

```
L.__proto__ === R.prototype
```

## 7、继承
### 构造函数继承
可传参，可继承父类实例方法和属性，但无法继承原型方法/属性
```
Parent.call(this, arg1, arg2)
```
### 原型链继承
不能传参，属性和方法是公用的，容易被篡改
```
Child.prototype = new Parent()
```
### 组合继承
重复调用父类，消耗性能
```
Parent.call(this, arg1, arg2) 
Child.prototype = new Parent()
```
### 原型式继承 ==> Object.create()
### 寄生式继承 ==> clone = Object.create() + return clone
### 寄生组合式继承
```
// 圣杯模式
function _inherits(Child, Parent){ 
	var prototype = Object.create(Parent.prototype); // 创建对象，创建父类原型的一个副本
	Child.prototype = prototype; // 指定对象，将新创建的对象赋值给子类的原型 
	prototype.constructor = Child; // 增强对象，弥补因重写原型而失去的默认的constructor 属性
}
Parent.call(this, arg1, arg2)
_inherits(Child, Parent)
```
### ES6 extends ==> Parent.call(this, arg1, arg2) + _inherits(Child, Parent) + Object.setPrototypeOf()
1. 可向父类传参
2. 继承属性和方法
3. 继承静态方法 Object.setPrototypeOf ? Object.setPrototypeOf(Child, Parent)  : Child.__proto__ = Parent;

## 8、this指向
* 默认绑定：window / undefined
* 对象调用：对象
* call/apply/bind手动修改
* new：实例对象
* 箭头函数：所在上下文

## 9、类型转换
* `-、*、/、% `：一律转换成数值后计算
* `+`：
    * 数字 + 字符串 = 字符串， 运算顺序是从左到右 ``` 1 + 'a' === '1a' ```
    * 数字 + 对象， 优先调用对象的valueOf -> toString ``` 1 + {a: 1} === '1[object Object]' ```
    * 数字 + boolean/null -> 数字 ``` 1 + true === 2 ```
    * 数字 + undefined -> NaN ``` 1 + undefined === NaN ```
* ``` [1].toString() === '1' ```
* ``` {}.toString() === '[object object]' ```
* ``` NaN !== NaN; +undefined === NaN ```

## 10、类型判断
* 基本类型：typeof ，除了`null`返回object，判断`function` 返回function，其他返回object
* 引用类型：
    * instanceof
    * Object.prototype.toString.call(obj) ==> '[object xxx]'
    * Array.isArray()

## 11、对象拷贝
### 浅拷贝：
* Object.assign()
* `...`展开运算符

### 深拷贝：完全拷贝一个新的对象，修改不会影响
* JSON.pares(JSON.stringify(obj)) 
    * 无法解决循环依赖
    * 无法解决值为函数、Symbol
* 递归根据类型逐一赋值

## 12、模块化
### commonjs: module.exports / require
* 输出的是值拷贝，模块内部改变不影响该值。
* 运行时加载。
* 服务端模块加载是同步加载，浏览器端（借助Browserify），模块需要提前编译打包。
* **循环依赖：** 已加载的模块会被缓存

### ES6: export / import
* 输出的是值的引用，不缓存该值。
* 编译时加载。
* **循环依赖：** 
    * import被JS引擎*静态分析*，优于其他模块其他内容执行 ==> 静态执行
    * export输出的接口，与其对应的值动态绑定，获取模块内部的值 ==> 动态绑定

## 13、防抖和节流
### 防抖 
> 多次高频操作，改为只在最后一次执行 ==> 例如：用户输入

```
function debounce(fn, wait, immediate) {
    let timer = null

    return function() {
        let args = arguments
        let context = this

        if (immediate && !timer) {
            fn.apply(context, args)
        }

        if (timer) clearTimeout(timer)
        timer = setTimeout(() => {
            fn.apply(context, args)
        }, wait)
    }
}
```

### 节流
> 每隔一段时间执行一次，降低频率 ==> 例如：滚动条

```
function throttle(fn, wait, immediate) {
    let timer = null
    let callNow = immediate
    
    return function() {
        let context = this,
            args = arguments

        if (callNow) {
            fn.apply(context, args)
            callNow = false
        }

        if (!timer) {
            timer = setTimeout(() => {
                fn.apply(context, args)
                timer = null
            }, wait)
        }
    }
}
```

## 14、ES6/ES7
* let / const: 块级作用域、不存在变量提升、暂时性死区、不允许重复声明
* 解构赋值
* class / extend: 类声明与继承
* Set / Map: 新的数据结构
* 异步解决方案：
    * promise
    * async/await

## 15、AST（抽象语法树）
### babel解析
* babylon: ES6/ES7 ==> AST 
* babel-traverse: AST ==> 新的AST
* babel-generator: 新的AST ==> ES5

## 16、数组
### 改变自身的数组方法
* push/pop: 末尾推入和弹出，返回推入/弹出项
* unshift/shift: 头部推入和弹出，返回操作项
* sort/reverse: 排序与反转
* **splice(start, number, value...):** 返回删除元素组成的数组，value 为插入项，改变原数组

### 不改变自身的数组方法
* concat: 浅拷贝
* join: 返回字符串
* forEach: 无法break，可以用try/catch中throw new Error来停止
* map: 返回新数组
* reduce/reduceRight(fn(prev, cur)， defaultPrev)
* filter: 过滤
* some: 有一项返回true，则整体为true
* every: 有一项返回false，则整体为false
* **slice(start, end):** 返回截断后的新数组
* flat: 数组扁平化
    1. array.flat(Infinity)
    2. array.toString().split(',')
    3. JSON.parse(`[${JSON.stringify(array).replace(/(\[|\])/g, '')}]`)
    4. while(arr.some(Array.isArray)) { array = [].concat(...array) }
    5. reduce
    6. 递归





