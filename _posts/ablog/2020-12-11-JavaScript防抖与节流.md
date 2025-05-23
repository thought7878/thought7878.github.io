---
layout: post
title: JavaScript防抖与节流
subtitle: 
categories: react
tags:
  - JavaScript
top: 3
---

## Introduction

防抖与节流是前端开发中常用的性能优化手段，本文将详细介绍防抖与节流的概念、应用场景、实现方式以及优缺点。

## 防抖（Debounce）

简单来说，防抖函数会**推迟函数的执行，直到过了一定时间间隔后，函数被执行**。如果在这个时间间隔内再次调用该函数，则重新计时。

### 应用场景

1. 搜索框输入联想
   当用户输入搜索关键字时，不会立即发送请求，而是等待用户输入完成后再发送请求。
2. 按钮提交事件
   防止用户在短时间内多次点击提交按钮，导致多次提交表单。
3. 浏览器窗口调整事件
   当用户调整浏览器窗口大小时，不会立即触发 resize 事件，而是等待用户停止调整窗口大小后再触发。当然这种情况使用节流可能更合适，后文会详细介绍。

### 实现方式

1. 方法1： 使用setTimeout（推荐）

```javascript
function debounce(fn, delay) {
  let timer
  // 返回一个debounce后的函数
  return function () {
    // 保存函数调用时的上下文和参数
    const context = this
    const args = arguments
    // 每当函数再次被调用，清除之前的定时器
    clearTimeout(timer)
    // 需要一个定时器来记录延迟执行的函数
    timer = setTimeout(() => {
      fn.apply(context, args)
    }, delay)
  }
}
```

2. 方法2: 使用时间戳

```javascript
function debounce(fn, delay) {
  let lastCall = 0 //初始化上次调用时间为0
  let timer

  return function () {
    const now = Date.now() // 获取当前时间戳
    const context = this
    const args = arguments

    clearTimeout(timer) // 每次调用都清除上次的定时器，即使定时器一开始为空

    // 为了让每次第一次调用时延迟执行，符合防抖的定义
    if (now - lastCall >= delay) {
      lastCall = now
      timer = setTimeout(() => {
        fn.apply(context, args)
      }, delay)
    } else {
      timer = setTimeout(() => {
        lastCall = now
        fn.apply(context, args)
      }, delay)
    }
  }
}
```

试着使用一下：

```javascript
function sayHi() {
  console.log('Hello World')
}

const debounced = debounce(sayHi, 1000)

debounced() // 1 秒后输出 'Hello World'
```

我们来设置一个场景，在没有防抖的情况下，鼠标划过一个元素，会打印`hello`:

```html
<div id="app">Hello</div>
```

```javascript
const app = document.getElementById('app')

app.addEventListener('mouseover', () => {
  console.log('hello')
})
```

使用防抖函数后：

```javascript
const app = document.getElementById('app')

app.addEventListener(
  'mousemove',
  debounce(() => {
    console.log('hello')
  }, 1000)
)

function debounce(fn, delay) {
  let timer
  // 返回一个debounce后的函数
  return function () {
    // 保存函数调用时的上下文和参数
    const context = this
    const args = arguments
    // 每当函数再次被调用，清除之前的定时器
    clearTimeout(timer)
    // 需要一个定时器来记录延迟执行的函数
    timer = setTimeout(() => {
      fn.apply(context, args)
    }, delay)
  }
}
```

鼠标不停滑动，不打印，停下1秒后打印`hello`。

### 加强版防抖

如果我们希望在防抖函数中添加立即执行的功能，可以通过设置一个 `immediate` 参数来实现。

```javascript
function enhance_debounce(func, wait, immediate) {
  var timer

  return function () {
    var context = this
    var args = arguments

    if (immediate) {
      //如果设置了immediate为true，立即执行
      let callNow = !timer
      clearTimeout(timer)
      timer = setTimeout(function () {
        func.apply(context, args) // 时间到之后，执行函数
        timer = null // 清除计时器的引用
        // clearTimeout(timer) 必须使用timer = null来清除引用，而不能使用clearTimeout(timer)，否则会导致timer一直有值，无法再次进入if (callNow)的判断
      }, wait)
      if (callNow) func.apply(context, args) // 在wait时间内，第一次触发时，立即执行
    } else {
      // 普通的防抖
      clearTimeout(timer)
      timer = setTimeout(function () {
        func.apply(context, args)
      }, wait)
    }
  }
}
```

还是刚刚那个场景

```javascript
const app = document.getElementById('app')

app.addEventListener(
  'mousemove',
  enhance_debounce(
    () => {
      console.log('hello')
    },
    1000,
    true
  )
)
```

当我们设置`immediate`为`true`时，鼠标第一次进入元素时会立即打印`hello`，之后的行为和普通防抖一样。
当设置的时间过去之后，我们再次划入元素，还是会立即打印`hello`，并且在无划动的情况下，1秒后再次打印`hello`。

### 优缺点

- 优点：防抖函数可以有效减少函数的执行次数，提高性能。
- 缺点：如果函数需要立即执行，可能会导致延迟执行的问题，但是通过加强版防抖函数可以解决这个问题。

## 节流（Throttle）

节流函数会**限制函数的执行次数，确保函数在一定时间间隔内只执行一次**。例如，如果设置一个 1000ms 的节流函数，那么在 1000ms 内，无论调用多少次，函数只会执行一次。

### 应用场景

1. 埋点上报
   在用户滚动页面时，需要上报滚动位置，但是不希望频繁上报，可以使用节流函数。
2. 拖拽事件
   在拖拽元素时，需要频繁计算元素的位置，我们可以使用节流函数来减少计算次数，提高性能。

### 实现方式

1. 使用isThrottle标志

```javascript
function throttle(fn, interval) {
  let isThrottled = false
  return function () {
    const context = this
    const args = arguments
    if (!isThrottled) {
      fn.apply(context, args)
      isThrottled = true
      setTimeout(() => {
        isThrottled = false
      }, interval)
    }
  }
}
```

2. 使用时间戳

````javascript
function throttle(fn, interval) {
  let lastCall = 0
  return function() {
    let now = Date.now()
    const context = this
    const args = arguments

    if(now - lastCall >= interval ) {
      fn.apply(context, args)
      lastCall = now
    }
  }
}

我们还是在上面的场景中使用节流函数：

```javascript
const app = document.getElementById('app')

app.addEventListener(
  'mousemove',
  throttle(() => {
    console.log('hello')
  }, 1000)
)
````

鼠标不停滑动，1秒内只打印一次`hello`，非常好。
问题在于，当我们停下鼠标后，最后一次的`hello`不会打印，因为我们设置的是1秒内只执行一次。

比如在下面这个搜索框中，用户在0-1秒内输入了`a`，在1-1.5秒内输入了`b`和`c`，之后停止了输入，我们就会丢失`c`这个输入，因为它在第二个1秒内被忽略了。

```javascript
const input = document.getElementById('input')
input.addEventListener(
  'input',
  throttle((e) => {
    console.log(input.value)
  }, 1000)
)
```

```html
<input type="text" id="input" />
```

### 加强版节流

加强版的节流加入了 `debounce`的兜底功能，既能避免函数频繁调用，又能确保在用户停止操作后执行一次函数。

```javascript
function enhanced_throttle(func, wait) {
  let lastTime = 0
  let timer = null

  return function () {
    const context = this
    const args = arguments
    let now = Date.now()

    if (now - lastTime < wait) {
      clearTimeout(timer) // debounce的推迟执行效果
      timer = setTimeout(() => {
        lastTime = now
        func.apply(context, args)
      }, wait)
    } else {
      lastTime = now
      func.apply(context, args)
    }
  }
}
```

用户在`0-1`秒输入`a`，在`1-1.5`秒输入`b`和`c`，之后停止输入，我们能够打印出`abc`。

## 总结

防抖和节流是前端开发中常用的性能优化手段，可以有效减少函数的执行次数，提高性能。防抖函数会推迟函数的执行，直到过了一定时间间隔后，函数被执行；节流函数会限制函数的执行次数，确保函数在一定时间间隔内只执行一次。
加强版的防抖在原有的基础上增加了立即执行的功能，加强版的节流在原有的基础上增加了 `debounce` 的兜底功能，保证最后一次的执行。