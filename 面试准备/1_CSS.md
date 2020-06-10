# 面试准备1 - CSS篇

## 1、盒模型
> CSS对应的属性是`box-sizing`

* box-sizing: border-box ==> IE模型
* box-sizing: content-box ==> 标准盒模型

## 2、BFC
> BFC ==> 会计格式化上下文，实质就是一个独立的区域，内外的元素不会互相影响。

### 触发条件
* 根元素
* position: absolute/fixed
* float:
* display: inline-block ==> 阻止margin重叠
* overflow: hidden ==> 清除浮动
* flex: 

## 3、层叠上下文
**同一层叠上下文顺序:**
1. background/border
2. z-index: 负数
3. block元素
4. float元素
5. inline元素
6. z-index: 0/auto
7. z-index: 正数

## 4、居中布局
### 水平居中
* 块元素：margin: 0 auto
* 行内元素：text-align: center

### 垂直居中
* line-height: height

### 水平垂直居中
* display: flex; + justify-content: center; + align-items: center
* position: absolute; left: 50%; top: 50%; transform: translate(-50%,-50%);
* position + 负margin（宽高已知）
* position + 各方向距离为0 + marigin: auto （宽高已知）

## 5、选择器优先级
* !important > 行内 > id > class > 标签 > * > 继承 > 默认

## 6、去除浮动，防止父级高度塌陷
* 伪类：==> :after{}
* 额外加一个div，clear:both
* 触发父盒子BFC，overflow:hidden

## 7、CSS动画
### transition：过渡动画
> 常用钩子：`transitionend`

```
// 动画属性 动画过渡需要多少时间 动画运动速度曲线 动画延迟时间
transition: all 2s ease 1s
```
### animation：
> 常用钩子：`animationend`

```
// 动画名称 动画过渡需要多少时间 动画运动速度曲线 动画延迟时间 动画执行次数 动画是否反响
animation: animationname 2s ease 1s 3/infinite
// animation-direction: 方向
// animation-fill-mode: 静止模式
@keyframes animationname
{
    from {top:0px;}
    to {top:200px;}
}
```

## 8、flex布局
**容器：**
* display: flex
* flex-direction: row / column
* flex-wrap: wrap / no-wrap
* flex-flow: flex-direction || flex-wrap
* justify-content: center / space-etween
* align-items: center / stretch
* align-content: center / stretch

**子项目：**
* flex: 0 1 auto
* order: <number>
* flex-glow: 放大比例
* flex-shrink: 缩小比例
* flex-basis: 自身大小
* align-self: center / stretch

## 9、移动端适配


