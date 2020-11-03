## 常见基础题
### this指向
this 是执行上下文中的一个属性，它指向最后一次调用这个方法的对象。在实际开发中，this 的指向可以通过四种调用模
式来判断。

1. 函数调用模式，当一个函数不是一个对象的属性时，直接作为函数来调用时，this 指向全局对象。
2. 对象方法调用模式，如果一个函数作为一个对象的方法来调用时，this 指向这个对象。
3. 构造器调用模式，如果一个函数用 new 调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象。
4. apply 、 call 和 bind 调用模式，这三个方法都可以显示的指定调用函数的 this 指向。其中 apply 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。call 方法接收的参数，第一个是 this 绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用 call() 方法时，传递给函数的参数必须逐个列举出来。bind 方法通过传入一个对象，返回一个 this 绑定了传入对象的新函数。这个函数的 this 指向除了使用 new 时会被改变，其他情况下都不会改变。

特殊情况：箭头函数的this默认绑定外层this，且不能使用call、apply、bind等方法改变

### js事件循环
js主线程它是有一个执行栈，所有的js代码都会在执行栈里运行。在执行代码过程中，如果遇到一些异步代码(比如setTimeout,ajax,promise.then以及用户点击等操作),那么浏览器就会将这些代码放到定时触发器线程、异步http请求线程、事件触发线程来执行。事件循环执行流程如下:
1. 检查 Macrotask 队列是否为空,若不为空，则进行下一步，若为空，则跳到3
2. 从 Macrotask 队列中取队首(在队列时间最长)的任务进去执行栈中执行(仅仅一个)，执行完后进入下一步
3. 检查 Microtask 队列是否为空，若不为空，则进入下一步，否则，跳到1（开始新的事件循环）
4. 从 Microtask 队列中取队首(在队列时间最长)的任务进去事件队列执行,执行完后，跳到3
其中，在执行代码过程中新增的microtask任务会在当前事件循环周期内执行，而新增的macrotask任务只能等到下一个事件循环才能执行了。

### 原型链
```javascript
'1'.__proto__ === String.prototype
String.prototype.__proto__ === Object.prototype
Object.prototype.__proto__ === null

String.__proto__ === Function.prototype
Function.prototype.__proto__ === Object.prototype // Function.prototype is a function

Function.__proto__ === Function.prototype 
Object.__proto__ == Function.prototype
```

### 箭头函数和普通函数的区别
1. 箭头函数没有`prototype`
2. 普通函数的`this`是在执行期间确认的：谁调用指向谁；箭头函数是在定义期间就确认的，箭头函数的`this`继承自外层第一个普通函数的this，如果没在函数内指向`window`
3. 箭头函数不能被`new`调用，`JavaScript`函数有两个内部方法：`[[Call]]`和`[[Construct]]`，当通过`new`调用函数时，执行`[[Construct]]`方法，创建一个实例对象，然后再执行函数体，将 this 绑定到实例上。当直接调用的时候，执行`[[Call]]`方法，直接执行函数体。箭头函数没有`[[Construct]]`方法，所以使用`new`调用会报`TypeError: aaa is not a constructor`
4. 箭头函数不能用`call()`、`apply()`、`bind()`方法修改里面的this
5. 箭头函数没有自己的`arguments`对象，箭头函数可以访问外围函数的`arguments`对象，全局情况下访问`window.arguments`
6. 箭头函数new.target指向外层普通函数的引用
7. 箭头函数写法更简洁