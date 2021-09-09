### 缺点

1、一旦新建就会立即执行，无法中途取消

2、如果不设置回调函数,Promise内部抛出的错误，不会反应到外部

3、当处于pending状态时，无法得知目前进展到哪一个阶段

### 基本用法

`ES6`规定，Promise对象时一个构造函数，用来生成Promise实例

```js
conse promise = new Promise(function(resolve, reject){
    if (/* 异步操作成功*/) {
        resolve(value);
	}else{
         reject(value);                  
     }
})
```

Promise 新建后就会立即执行。

```js
let promise = new Promise(function(resolve, reject) {
  console.log('Promise'); // 同步代码
  resolve();
});
promise.then(function() {
  console.log('resolved.');
});
console.log('Hi!');
// Promise
// Hi!
// resolved
```

上面代码中，Promise 新建后立即执行，所以首先输出的是`Promise`。然后，`then`方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以`resolved`最后输出。

注意，调用`resolve`或`reject`并不会终结 Promise 的参数函数的执行。

```js
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
  reject(); // 没有效果，因为状态已经变成了resolved  所以不能再次改变promise的状态了
  console.log(3);
}).then(r => {
  console.log(r);
});
// 2
// 3
// 1
```

上面代码中，调用`resolve(1)`以后，后面的`console.log(2)`还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。



### `Promise.prototype.then()`

它的作用时为Promise实例添加**状态改变时**的回调函数，then方法接受两个可选的参数，第一个参数是resolved状态的回调函数（`onResolved`处理程序），第二个参数时rejected状态的回调函数(`onRejected`处理程序)，传给then（）的任何非函数类型的参数都会默认忽略。如果指向提供`onRejected`处理程序，那要在`onResolved`的位置传入`undefined`。

### `Promise.prototype.catch()`

用于指定**发生错误时**的回调函数（`onRejected`处理程序），。事实上，这个方法就是一个语法糖，调用它就相当于调用 `Promise.prototype. then(null, onRejected)`

1、没有报错的情况下都是返回`resolved`状态的`promise` || `Promise.resolved()` 触发`then`回调

2、报错的情况下返回`reject`状态的`promise` || **`Promise.reject()`** 等同于抛出异常，触发`catch`回调

3、`async`函数返回的是一个`promise`对象    `await`相当于`promise`的`then`

### `Promise.resolve()`

```js
Promise.resolve('foo')
// 等价于
new Promise(resolve=>resolve('foo'))
```

`Promise.resolve()`方法的参数分成四种情况。

1) 参数是一个Promise实例，那么`Promise.resolve`将不做任何修改，原封不动的返回这个实例

2）参数是一个`thenable`对象，指的是具有then方法的对象，比如下面这个对象

```js
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};
```

`Promise.resolve（）`方法会将这个对象转为Promise对象，然后就立即执行`thenable`中的then（）方法

```js
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function (value) {
  console.log(value);  // 42
});
```

`thenable`对象的then()方法执行后，对象p1的状态就变为了resolved，从而立即执行最后的then方法指定的回调函数，输出42。

3）参数不是具有then()方法的对象，或者根本就不是对象，`Promise.resolve()`会返回一个新的Promise对象，状态为resolved

```js
const p = Promise.resolve('hello')
p.then(function(s){
    console.log(s)
});
// hello
```

4）不带有任何参数     直接返回一个resolved状态的promise对象

```js
const p = new Promise();
p.then(function(){
 // ...
})
```

需要注意的是，立即resolve（）的promise对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮事件循环开始时

```js
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');
// one
// two
// three
```

上面代码中，`setTimeout(fn, 0)`在下一轮“事件循环”开始时执行，`Promise.resolve()`在本轮“事件循环”（所有同步任务）结束时执行，`console.log('one')`则是立即执行，因此最先输出。

### `Promise.reject()`

`Promise.reject(reason)`方法也会返回一个新的 Promise 实例，该实例的状态为`rejected`。

```js
const p = Promise.reject('出错了')；
// 等价于
const p = new Promise((resolve,reject)=>{reject('出错了')})
p.then(null, function(s){
    console.log(s)
})
// 出错了
```

上面代码生成一个 Promise 对象的实例`p`，状态为`rejected`，回调函数会立即执行。

`Promise.reject()`方法的参数，会原封不动地作为`reject`的理由，变成后续方法的参数。



## 异步函数 `ES8的async/await`

## 1、async

`async`关键字可以让函数具有异步特征，但总体上代码还是同步执行的，异步函数如果不包含await关键字，执行上和普通函数没有什么区别，只不过使用return会返回一个期约对象而已。

```js
async function foo() {   
    console.log(1);
}
foo();
console.log(2); 
// 1
// 2
```

不过，异步函数如果使用return关键字返回了值（没有return则返回`undefined`），这个值会被`Promise.resolve()`包装成一个期约对象，异步函数始终返回一个期约对象。

```js
async function foo() {
  console.log(1); 
    return 3; // 等价于 return Promise.resolve(3)
}
// 给返回的期约添加一个解决处理程序 foo().then(console.log);
console.log(2);
// 1 
// 2
// 3
```

then() 处理程序实现给promise对象解包！！！

**疑惑**

为什么拒绝期约的错误不会被异步函数捕获

```js
async function foo() {   
    console.log(1);
  throw 3;
}
// 给返回的期约添加一个拒绝处理程序 
foo().catch(console.log); 
console.log(2);
// 1 
// 2 
// 3
// 不过，拒绝期约的错误不会被异步函数捕获：
async function foo() {   
    console.log(1);
	Promise.reject(3); 
}
// Attach a rejected handler to the returned promise 
foo().catch(console.log);
console.log(2); 
// 1
// 2
// Uncaught (in promise): 3
```

## 2、await

`await`关键字会暂停执行异步函数后面的代码，让出JavaScript运行时的执行线程。`await`关键字同样是尝试“解包”期约对象的值，然后将这个值传给表达式，再恢复异步函数的执行。await必须在异步函数`async`中使用

```js
// 等待一个原始值
async function foo() {
  console.log(await 'foo'); 
}
foo(); 
// foo

// 等待一个期约
async function qux() {
  console.log(await Promise.resolve('qux'));
}
qux();                                                                                
// qux
```

抛出错误，会返回拒绝的期约

```js
async function foo() {   
    console.log(1);
	await (() => { throw 3; })();
}
// 给返回的期约添加一个拒绝处理程序
foo().catch(console.log); 
console.log(2);
// 1
// 2 
// 3
```

如前面的例子所示，单独的  `Promise.reject()`不会被异步函数捕获，而会抛出未捕获错误。不 过，对拒绝的期约使用 await则会释放（unwrap）错误值（将拒绝期约返回）

```js
async function foo() {
    console.log(1);
    await Promise.reject(3); 
    console.log(4); // 这行代码不会执行
}
// 给返回的期约添加一个拒绝处理程序 
foo().catch(console.log); 
console.log(2);
// 1
// 2
// 3
```









