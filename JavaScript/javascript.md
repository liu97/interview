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
```javascript
const a = new Promise((resolve, reject) => {
    console.log('promise1')
    resolve()
}).then(() => {
    console.log('promise2')
})
setTimeout(function(){
  console.log('setTimeout1');
  Promise.resolve().then(()=>{
    console.log('resolve1')
  })
},0)
setTimeout(function(){
   console.log('setTimeout2');
   Promise.resolve().then(()=>{
    console.log('resolve2')
  })
},0)
const b = new Promise(async (resolve, reject) => {
    await a
    console.log('after1')
    await b
    console.log('after2')
    resolve()
})
console.log('end')
// promise1 end promise2 after1 setTimeout1 resolve1 setTimeout2 resolve2
```

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

### Promise
Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是 pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者 rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。状态的改变是通过 resolve() 和 reject() 函数来实现的，我们
可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。

### 多个异步任务依次执行
```javascript
function asyncFunction1(){
  return new Promise((resolve, reject)=>{
    setTimeout(()=>{
      resolve('asyncFunction1')
    }, 1000)
  })
}
function asyncFunction2(){
  return new Promise((resolve, reject)=>{
    setTimeout(()=>{
      resolve('asyncFunction2')
    }, 1000)
  })
}
function asyncFunction3(){
  return new Promise((resolve, reject)=>{
    setTimeout(()=>{
      resolve('asyncFunction3')
    }, 1000)
  })
}

function orderPrint(){
  asyncFunction1().then(()=>{
    return asyncFunction2()
  }).then(()=>{
    return asyncFunction3()
  })
}
```

### 数组扁平化，可以控制展开深度
```javascript
function deepFlatten(value, layer = 1){
    let result = []
    function loop(start, arr = [], l = 0){
        if(l <= layer){
            l++;
            for(let item of start){
                if(Array.isArray(item)){
                    loop(item, result, l);
                }else{
                    result.push(item);
                }
            }
        }else{
            result.push(start);
        }
    }
    if(Array.isArray(value)){
        loop(value);
        return result
    }else{
        return value
    }
    
}
let r = deepFlatten([1, [2, [3, [4, 5], 6], 7], 8], 2)
```

### 模拟new运算符操作
```javascript
function isObject(obj){
  return typeof obj === 'object' && obj !== null
}
function isFunction(fun){
  return typeof fun === 'function'
}
function newOperator(fun, ...args){
  if(!isFunction(fun)){
    return throw Error(`${JSON.stringify(fun)} is not a constructor`)
  }
  let obj = Object.create(fun.prototype)
  let back = fun.call(obj, ...args)
  if(isObject(back) || isFunction(back)){
    return back
  }else{
    return obj
  }
}
```

### 实现防抖函数
```javascript
function debounce(fun, wait = 0, immediate = false){
  if(typeof fun != 'function'){
    throw Error(`${JSON.stringify(fun)} is not a function`);
  }

  let timer = null;
  return function(...args){
    clearTimeout(timer);
    if(immediate){
      if(timer === null){
        fun.apply(this, args);
      }
      timer = setTimeout(()=>{
        timer = null;
      }, wait)
    }else{
      timer = setTimeout(fun.bind(this, ...args), wait)
    }
  }
}
```

### 实现节流函数
```javascript
function throttle(fun, wait = 0){ // 时间戳实现
  if(typeof fun != 'function'){
    throw Error(`${JSON.stringify(fun)} is not a function`);
  }

  let oldTime = new Date();
  return function(...args){
    let nowTime = new Date();
    if(nowTime - oldTime >= wait){
      fun.apply(this, args);
      oldTime = nowTime;
    }
  }
}

function throttle(fun, wait){ // 定时器实现
  if(typeof fun != 'function'){
    throw Error(`${JSON.stringify(fun)} is not a function`);
  }

  let timer = null;
  return function(...args){
      if(timer === null){
        timer = setTimeout(()=>{
          fun.apply(this, args);
          timer = null;
        } ,wait)
      }
  }
}
```

### 异步reject自动重试
```javascript
// 使用autoRetry()包裹方法，并给出最大重试次数（执行数=重试次数+1）
function foo(param) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        try{
          JSON.parse('{{'); // 执行到这里会报错
          return resolve(param)
        } catch (err) {
          return reject(err)
        }
      }, 1000)
    })
  }
function autoRetry(fun, time){
    let curTime = 0;
    return function result(...arg){
        return new Promise((resolve, reject) => {
            fun(...arg).then((data)=>{
                resolve(data);
            }).catch((err)=>{
                curTime++
                if(curTime <= time){
                    resolve(result(...arg));
                }else{
                    reject(err)
                }
            })
        })
    }
}
  
let func = autoRetry(foo, 3);
func({ a: 1, b: 1 }).then(function (res) {
  console.log(res)
}, function (err) {
  console.log(err)
})
```

### 最接近值
```javascript
// 给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。
// 假定每组输入只存在唯一答案。 示例： 输入：nums = [-1,2,1,-4], target = 1 输出：2 解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
function getNear(nums, target){
  nums.sort();
  let best = Infinity,
      length = nums.length;
  for(let i = 0; i < length; i++){
    let j = i+1,
        k = length-1;
    while(j < k){
      let sum = nums[i]+nums[j]+nums[k];
      if(Math.abs(sum-target) < Math.abs(best)){ // 获取更接近值
        best = sum-target;
      }

      if(sum-target === 0){ // 找到相等值
        return target;
      }else if(sum-target < 0){
        j++;
      }else{
        k--;
      }
    }
  }

  return best+target;
}
```

### 相交链表
```javascript
// 找到两个单链表相交的起始节点，假定两链表一定有相交节点

function node(val, next) {
  this.val = val
  this.next = next
}

function findNode(root1, root2){
  if(!root1 || !root2) return null
	let head1 = root1, head2 = root2
  while(head1 !== head2){
      head1 = head1.next === null ? root2 : head1.next
			head2 = head2.next === null ? root1 : head2.next
  }
  
  return head1
}
```