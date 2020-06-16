# JS篇 - 变量和类型

## 1、变量声明
* var: 函数作用域、可重复声明、变量提升
* let: 块级作用域、不能重复声明、没有变量提升 ==> Reference Error(暂时性死区)
* const: 声明常量

## 2、基本类型和引用类型
* 基本类型：Number、String、Boolean、Null、Undefined、Symbol（ES6）、BIgInt（ES10）
* 引用类型：Object、Array、Regexp、Date、Function、Math

## 3、判断数据类型
* typeof: 只能检测基本类型（null除外）
* arr instanceof Array: 只能检测出引用类型
* Object.prototype.toString.call(obj): 性能不好
* Array.isArray: 只能检测数组
* o.constructor === Object: 易被改写，null和undefined不能检测，不建议使用

## 4、类型转换
* `-、*、/、% `：一律转换成数值后计算
* `+`：
    * 数字 + 字符串 = 字符串， 运算顺序是从左到右 ``` 1 + 'a' === '1a' ```
    * 数字 + 对象， 优先调用对象的valueOf -> toString ``` 1 + {a: 1} === '1[object Object]' ```
    * 数字 + boolean/null -> 数字 ``` 1 + true === 2 ```
    * 数字 + undefined -> NaN ``` 1 + undefined === NaN ```
* ``` [1].toString() === '1' ```
* ``` {}.toString() === '[object object]' ```
* ``` NaN !== NaN; +undefined === NaN ```

## 5、0.1 + 0.2 !== 0.3?
> JS中所有数字是按照`双精度64位浮点格式`表示，-(2^53-1) ～ (2^53-1)，超出计算则丢失精度