call + bind + apply
===

> Create by **jsliang** on **2020-09-08 13:37:27**  
> Recently revised in **2020-10-08 10:23:42**

<!-- 目录开始 -->
## <a name="chapter-one" id="chapter-one"></a>一 目录

**不折腾的前端，和咸鱼有什么区别**

| 目录 |
| --- |
| [一 目录](#chapter-one) |
| <a name="catalog-chapter-two" id="catalog-chapter-two"></a>[二 前言](#chapter-two) |
| <a name="catalog-chapter-three" id="catalog-chapter-three"></a>[三 Arguments 对象](#chapter-three) |
| <a name="catalog-chapter-four" id="catalog-chapter-four"></a>[四 call](#chapter-four) |
| <a name="catalog-chapter-five" id="catalog-chapter-five"></a>[五 手写 call](#chapter-five) |
| <a name="catalog-chapter-six" id="catalog-chapter-six"></a>[六 apply](#chapter-six) |
| <a name="catalog-chapter-seven" id="catalog-chapter-seven"></a>[七 手写 apply](#chapter-seven) |
| <a name="catalog-chapter-eight" id="catalog-chapter-eight"></a>[八 bind](#chapter-eight) |
| <a name="catalog-chapter-night" id="catalog-chapter-night"></a>[九 手写 bind](#chapter-night) |
| <a name="catalog-chapter-ten" id="catalog-chapter-ten"></a>[十 题目](#chapter-ten) |
| &emsp;[10.1 this 指向问题 1](#chapter-ten-one) |
| &emsp;[10.2 this 指向问题 2](#chapter-ten-two) |
<!-- 目录结束 -->

## <a name="chapter-two" id="chapter-two"></a>二 前言

> [返回目录](#chapter-one)

`call`、`bind`、`apply` 区别：

* `call` 可以改变函数指向，第一个参数是要改变指向的对象，之后的参数形式是 `arg1, arg2...` 的形式
* `apply` 同 `call`，不同点在于第二个参数是一个数组
* `bind` 改变 `this` 作用域会返回一个新的函数，这个函数不会马上执行

参考：

* [x] [MDN - Arguments](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments)【阅读建议：5min】
* [x] [MDN - call](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)【阅读建议：5min】
* [x] [MDN - apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)【阅读建议：5min】
* [x] [MDN - bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)【阅读建议：5min】
* [x] [JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)【阅读建议：10min】
* [ ] [再来40道this面试题酸爽继续](https://juejin.im/post/6844904083707396109)
* [ ] [this、apply、call、bind](https://juejin.im/post/6844903496253177863)
* [ ] [面试官问：JS的this指向](https://juejin.im/post/5c0c87b35188252e8966c78a)
* [ ] [面试官问：能否模拟实现JS的call和apply方法](https://juejin.im/post/5bf6c79bf265da6142738b29)
* [ ] [面试官问：能否模拟实现JS的bind方法](https://juejin.im/post/5bec4183f265da616b1044d7)
* [ ] [JavaScript基础心法——this](https://github.com/axuebin/articles/issues/6)
* [ ] [JavaScript深入之从ECMAScript规范解读this](https://github.com/mqyqingfeng/Blog/issues/7)
* [ ] [前端基础进阶（七）：全方位解读this](https://www.jianshu.com/p/d647aa6d1ae6)
* [ ] [JavaScript深入之call和apply的模拟实现](https://juejin.im/post/5907eb99570c3500582ca23c)
* [ ] [JavaScript基础心法—— call apply bind](https://github.com/axuebin/articles/issues/7)
* [ ] [回味JS基础:call apply 与 bind](https://juejin.im/post/57dc97f35bbb50005e5b39bd)
* [ ] [不用call和apply方法模拟实现ES5的bind方法](https://github.com/jawil/blog/issues/16)
* [ ] [JavaScript中的this](https://juejin.im/post/59748cbb6fb9a06bb21ae36d)
* [ ] [this,this,再次讨论javascript中的this,超全面](https://www.cnblogs.com/painsOnline/p/5102359.html)
* [ ] [JS中函数名后面的括号加与不加的区别和作用？](https://www.zhihu.com/question/31044040)

## <a name="chapter-three" id="chapter-three"></a>三 Arguments 对象

> [返回目录](#chapter-one)
  
* [MDN - Arguments](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments)

`arguments` 是一个对应于传递给函数的参数的类数组对象。

```js
function fun(a, b, c) {
  console.log(arguments[0]); // 1
  console.log(arguments[1]); // 2
  console.log(arguments[2]); // 3
}
```

`arguments` 对象不是一个 `Array `。

它类似于 `Array`，但除了 `length` 属性和索引元素之外没有任何 `Array` 属性。

将 `arguments` 转为数组：

```js
// ES5
var arg1 = Array.prototype.slice.call(arguments);
var arg2 = [].slice.call(arguments);

// ES6
var arg3 = Array.from(arguments);
var arg4 = [...arguments];
```

## <a name="chapter-four" id="chapter-four"></a>四 call

> [返回目录](#chapter-one)

* [MDN - call](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

`call()` 方法使用一个指定的 `this` 值和单独给出的一个或多个参数来调用一个函数。

> 注意：该方法的语法和作用与 `apply()` 方法类似，只有一个区别，就是 `call()` 方法接受的是一个参数列表，而 `apply()` 方法接受的是一个包含多个参数的数组。

* 语法：`function.call(thisArg, arg1, arg2, ...)`
  * `thisArg`：可选的。在 `function` 函数运行时使用的 `this` 值。
  * `arg1, arg2, ...`：指定的参数列表

```js
function Product (name, price) {
  this.name = name;
  this.price = price;
}

function Food (name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}

const food = new Food('cheese', 5);
console.log(food.name); // 'cheese'
```

## <a name="chapter-five" id="chapter-five"></a>五 手写 call

> [返回目录](#chapter-one)

首先我们得搞明白 `call` 的特性：

1. 如果 `obj.call(null)`，那么 `this` 应该指向 `window`
2. 如果 `obj1.call(obj2)`，那么谁调用它，`this` 指向谁
3. `call` 可以传入多个参数，所以可以利用 `arguments` 这个字段来获取所有参数，然后转换数组后，去除第 1 个参数，获取其他的就行
4. 设置一个变量，用完可以删掉它

综上：

> 手写 call 的 JS 代码：

```js
Function.prototype.myCall = function(context) {
  // 设置 this 指向
  const newContext = context || window;
  newContext.fn = this;
  
  // 获取剩余参数
  const otherArg = Array.from(arguments).slice(1); // 也可以 [...arguments]
  newContext.fn(otherArg);
  const result = newContext.fn(otherArg);

  // 删除 newContext
  delete newContext;

  // 返回结果值
  return result;
};
```

> 防抖函数绑定手写 call

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>手写 call</title>
</head>
<body>
  <button class="btn">123</button>

  <script>
    (function() {
      Function.prototype.myCall = function(context) {
        const newContext = context || window;
        newContext.fn = this;
        const otherArg = Array.from(arguments).slice(1);
        newContext.fn(otherArg);
        const result = newContext.fn(otherArg);
        delete newContext;
        return result;
      };

      const debounce = function(fn) {
        let timer = null;
        return function() {
          clearTimeout(timer);
          timer = setTimeout(() => {
            fn.myCall(this, arguments);
          }, 1000);
        }
      }

      let time = 0;
      const getNumber = function() {
        console.log(++time);
      }

      const btn = document.querySelector('.btn');
      btn.addEventListener('click', debounce(getNumber));
    })()
  </script>
</body>
</html>
```

## <a name="chapter-six" id="chapter-six"></a>六 apply

> [返回目录](#chapter-one)

* [MDN - apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

`apply()` 方法调用一个具有给定 `this` 值的函数，以及以一个数组（或类数组对象）的形式提供的参数。

* 语法：`function.apply(thisArg, [argsArray])`
  * `thisArg`：必选的。在 `function` 函数运行时使用的 `this` 值
  * `[argsArray]`：可选的。一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 `func` 函数。

```js
const numbers = [5, 6, 2, 3, 7];

const max = Math.max.apply(null, numbers);
console.log(max); // 7

const min = Math.min.apply(null, numbers);
console.log(min); // 2
```

## <a name="chapter-seven" id="chapter-seven"></a>七 手写 apply

> [返回目录](#chapter-one)

```js
Function.prototype.myApply = function(context, arr) {
  // 绑定 this
  const newContext = context || window;
  newContext.fn = this;

  // 判断是否有参数
  if (!arr) {
    result = newContext.fn();
  } else {
    result = newContext.fn(arr);
  }

  // 删除这个元素
  delete newContext;

  // 返回结果
  return result;
};
```

用自定义 `apply` + 防抖：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>DOM 操作</title>
</head>
<body>
  <button class="btn">123</button>

  <script>
    (function() {
      Function.prototype.myApply = function(context, arr) {
        const newContext = context || window;
        newContext.fn = this;
        console.log(newContext.fn)
        if (!arr) {
          result = newContext.fn();
        } else {
          result = newContext.fn(arr);
        }

        delete newContext;
        return result;
      };

      const debounce = function(fn, number) {
        let timer = null;
        return function() {
          clearTimeout(timer);
          timer = setTimeout(() => {
            fn.myApply(this, number);
          }, 1000);
        }
      }

      const getNumber = function(time) {
        console.log(time);
      }

      let number = [1, 2, 3, 4, 5];
      const btn = document.querySelector('.btn');
      btn.addEventListener('click', debounce(getNumber, number));
    })()
  </script>
</body>
</html>
```

## <a name="chapter-eight" id="chapter-eight"></a>八 bind

> [返回目录](#chapter-one)

* [MDN - bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

`bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

* 语法：`function.bind(thisArg, arg1, arg2, ...)`
  * `thisArg`：调用绑定函数时作为 `this` 参数传递给目标函数的值。
  * `arg1, arg2, ...`：当目标函数被调用时，被预置入绑定函数的参数列表中的参数。
* 返回值：一个原函数的拷贝，并拥有指定的 `this` 值和初始参数

```js
const module = {
  x: 42,
  getX: function() {
    return this.x;
  },
};

const unboundGetX = module.getX;
console.log(unboundGetX()); // undefined
// 谁调用指向谁，这里 unboundGetX = module.getX
// 让 getX 里面的 this 指向了 window
// 而 window 里面并没有 x 方法
// 当然，在前面加上 window.x = 43 就有了

const boundGetX = unboundGetX.bind(module);
console.log(boundGetX()); // 42
// 通过 bind，将 this 指向 module
```

## <a name="chapter-night" id="chapter-night"></a>九 手写 bind

> [返回目录](#chapter-one)

```js
// MDN 的实现
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {

    // 如果这不是一个函数对象，报错
    if (typeof this !== 'function') {
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    // 设置变量
    var aArgs   = Array.prototype.slice.call(arguments, 1), // 获取第一个参数之后的所有元素
        fToBind = this, // 设置 this
        fNOP    = function() {}, // 设置空函数
        fBound  = function() {
          // this instanceof fBound === true 时,说明返回的 fBound 被当做 new 的构造函数调用
          return fToBind.apply(this instanceof fBound
            ? this
            : oThis,
            // 获取调用时（fBound）的传参.bind 返回的函数入参往往是这么传递的
            aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    // 维护原型关系
    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype; 
    }
    // 下行的代码使 fBound.prototype 是fNOP 的实例,因此
    // 返回的 fBound 若作为 new 的构造函数
    // new 生成的新对象作为 this 传入 fBound，新对象的 __proto__ 就是 fNOP 的实例
    fBound.prototype = new fNOP();

    return fBound;
  };
}
```

## <a name="chapter-ten" id="chapter-ten"></a>十 题目

> [返回目录](#chapter-one)
        
### <a name="chapter-ten-one" id="chapter-ten-one"></a>10.1 this 指向问题 1

> [返回目录](#chapter-one)
      
```js
var color = 'green';

var test4399 = {
  color: 'blue',
  getColor: function() {
    var color = 'red';
    console.log(this.color);
  },
};

var getColor = test4399.getColor;
getColor(); // 输出什么？
test4399.getColor(); // 输出什么？
```

---

答案：`green`、`blue`。

### <a name="chapter-ten-two" id="chapter-ten-two"></a>10.2 this 指向问题 2

> [返回目录](#chapter-one)

```js
var myObject = {
  foo: 'bar',
  func: function() {
    var self = this;
    console.log(this.foo);
    console.log(self.foo);
    (function() {
      console.log(this.foo);
      console.log(self.foo);
    })()
  }
}
myObject.func();
```

程序输出什么？

* A：bar bar bar bar
* B：bar bar bar undefined
* C：bar bar undefined bar
* D：undefined bar undefined bar

---

答案：C

1. 第一个 `this.foo` 输出 `bar`，因为当前 `this` 指向对象 `myObject`。
2. 第二个 `self.foo` 输出 `bar`，因为 `self` 是 `this` 的副本，同指向 `myObject` 对象。
3. 第三个 `this.foo` 输出 `undefined`，因为这个 IIFE（立即执行函数表达式）中的 `this` 指向 `window`。
4.第四个 `self.foo` 输出 `bar`，因为这个匿名函数所处的上下文中没有 `self`，所以通过作用域链向上查找，从包含它的父函数中找到了指向 `myObject` 对象的 `self`。

---

> <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">jsliang 的文档库</span> 由 <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/LiangJunrong/document-library" property="cc:attributionName" rel="cc:attributionURL">梁峻荣</a> 采用 <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享 署名-非商业性使用-相同方式共享 4.0 国际 许可协议</a>进行许可。<br />基于<a xmlns:dct="http://purl.org/dc/terms/" href="https://github.com/LiangJunrong/document-library" rel="dct:source">https://github.com/LiangJunrong/document-library</a>上的作品创作。<br />本许可协议授权之外的使用权限可以从 <a xmlns:cc="http://creativecommons.org/ns#" href="https://creativecommons.org/licenses/by-nc-sa/2.5/cn/" rel="cc:morePermissions">https://creativecommons.org/licenses/by-nc-sa/2.5/cn/</a> 处获得。