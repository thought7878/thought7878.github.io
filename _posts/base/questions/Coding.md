Question: What is the value of `foo`?  
问题： `foo` 的值是多少？

```js
var foo = 10 + '20';
```

Question: What will be the output of the code below?  
问题：下面的代码会输出什么？

```js
console.log(0.1 + 0.2 == 0.3);
```

Question: How would you make this work?  
问题：你将如何完成这项工作？

```js
add(2, 5); // 7
add(2)(5); // 7
```

Question: What value is returned from the following statement?  
问题：下面的语句返回什么值？

```js
"i'm a lasagna hog".split("").reverse().join("");
```

Question: What is the value of `window.foo`?  
问题： `window.foo` 的值是多少？

```js
( window.foo || ( window.foo = "bar" ) );
```

Question: What is the outcome of the two alerts below?  
问题：下面两个警报的结果是什么？

```js
var foo = "Hello";
(function() {
  var bar = " World";
  alert(foo + bar);
})();
alert(foo + bar);
```

Question: What is the value of `foo.length`?  
问题： `foo.length` 的值是多少？

```js
var foo = [];
foo.push(1);
foo.push(2);
```

Question: What is the value of `foo.x`?  
问题： `foo.x` 的值是多少？

```js
var foo = {n: 1};
var bar = foo;
foo.x = foo = {n: 2};
```

Question: What does the following code print?  
问题：下面的代码打印什么？

```js
console.log('one');
setTimeout(function() {
  console.log('two');
}, 0);
Promise.resolve().then(function() {
  console.log('three');
})
console.log('four');
```

Question: What is the difference between these four promises?  
问：这四个承诺有什么区别？

```js
doSomething().then(function () {
  return doSomethingElse();
});

doSomething().then(function () {
  doSomethingElse();
});

doSomething().then(doSomethingElse());

doSomething().then(doSomethingElse);
```

Question: What will the code below output to the console and why?  
问题：下面的代码将向控制台输出什么？为什么？

```js
(function(){
  var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));
```

Question: Consider the two functions below. Will they both return the same thing? Why or why not?  
问题：考虑下面的两个函数。他们都会返回相同的东西吗？为什么或者为什么不？

```js
function foo1()
{
  return {
      bar: "hello"
  };
}

function foo2()
{
  return
  {
      bar: "hello"
  };
}
```
