在掘金上阅读的一篇文章，写得很棒，就直接copy过来了

原链接: https://juejin.cn/post/6936630572936593422

# JS中的EventLoop、宏任务、微任务

## 相关知识点

- JS 中的线程
- EventLoop（事件循环机制）
- 宏任务和微任务

## JS 中的线程

### JS 为什么是单线程？

JS 单线程的特点就是同一时刻只能执行一个任务。

这是由一些与用户的互动以及操作 `DOM` 等相关的操作决定了 JS 要使用单线程，否则使用多线程会带来复杂的同步问题。如果是多线程，一个线程正在修改 DOM，另一个线程正在删除 DOM，那么以哪一个为准呢？

如果执行同步问题的话，多线程需要加锁，执行任务造成非常的繁琐。

> 注意：虽然 `HTML5` 标准规定，允许 `JavaScript` 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 `DOM` 。

### 单线程带来的问题

由于 `JavaScript` 是单线程的，单线程就意味着阻塞问题，当一个任务执行完成之后才能执行下一个任务。这样就会导致出现页面卡死的状态，页面无响应，影响用户的体验，所以不得不出现了同步任务和异步任务的解决方案。

- 同步任务：在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；
- 异步任务：不进入主线程，而进入"任务队列"的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行；

### JS 如何实现异步编程？

为了解决上述问题，出现了异步任务执行，它的运行机制如下：

- 所有同步任务都在主线程上执行，形成一个执行栈；
- 主线程之外，还存在一个"任务队列"。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件；
- 一旦执行栈中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行；
- 主线程不断重复上面的第三部；

> 注意：在主线程读取任务队列的时候，任务队列是一种“**先进后出**”的数据结构，排在前面的事件，优先被主线程读取。执行栈只要执行空，任务队列的第一个事件进入主线程。同时，如果存在定时器，主线程需要检查定时器执行的时间，到了时间才能给主线程执行。

## EventLoop - 事件循环机制

在深入事件循环机制之前，需要弄懂一下几个概念：

- 执行上下文(`Execution context`)
- 执行栈（`Execution stack`）
- 微任务（`micro-task`）
- 宏任务（`macro-task`）

### 执行上下文

执行上下文是一个抽象的概念，可以理解为是代码执行的一个环境。JS 的执行上下文分为三种，全局执行上下文、函数执行上下文、`Eval` 执行上下文。

- 全局执行上下文：这是默认或者说基础的上下文，任何不在函数内部的代码都在全局上下文中。它会执行两件事：创建一个全局的 `Window` 对象（浏览器的情况下），并且设置 `this` 的值等于这个全局对象，可以是外部加载的 JS 文件或者本地 `<scripe></script>` 标签中的代码。一个程序中只能有一个全局执行上下文；
- 函数执行上下文：每当一个函数被调用时，都会为该函数创建一个新的上下文。每个函数都有它自己的执行上下文，不过是在函数被调用时创建的。函数执行上下文可以有任意多个；
- `Eval` 函数执行上下文：执行在 `eval` 函数内部的代码也会有它属于自己的执行上下文；

### 执行栈

执行栈，就是我们数据结构中的“栈”，它具有“先进后出”的特点，正是因为这种特点，在我们代码进行执行的时候，遇到一个执行上下文就将其依次压入执行栈中。

当代码执行的时候，先执行位于栈顶的执行上下文中的代码，当栈顶的执行上下文代码执行完毕就会出栈，继续执行下一个位于栈顶的执行上下文。

```js
function foo() {
  console.log("a");
  bar();
  console.log("b");
}

function bar() {
  console.log("c");
}

foo();
复制代码
```

- 初始化状态，执行栈任务为空；
- `foo` 函数执行，`foo` 进入执行栈，输出 `a`，碰到函数 `bar`；
- 然后 `bar` 再进入执行栈，开始执行 `bar` 函数，输出 `c`；
- `bar` 函数执行完出栈，继续执行执行栈顶端的函数 `foo`，最后输出 `b`；
- `foo` 出栈，所有执行栈内任务执行完毕；

### 事件循环机制

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed1c5407c94f4a0792dbff71eef4b994~tplv-k3u1fbpfcp-watermark.image)

**上图解释：**

- 同步和异步任务分别进入不同的执行“场所”，同步的进入主线程，异步的进入 `Event Table` 并注册函数；
- 当指定的事情完成时，`Event Table` 会将这个函数移入`Event Queue`；
- 当栈中的代码执行完毕，执行栈（`call stack`）中的任务为空时，就会读取任务队列（`Event quene`）中的事件，去执行对应的回调；
- 如此循环，形成 JS 的事件循环机制（`Event Loop`）；

### 宏任务和微任务

先看一段代码的执行结果：

```js
console.log("script start");

setTimeout(function () {
  console.log("setTimeout");
}, 500);

Promise.resolve()
  .then(function () {
    console.log("promise1");
  })
  .then(function () {
    console.log("promise2");
  });

console.log("script end");
复制代码
```

输出结果为：

```js
script start
script end
promise1
promise2
setTimeout
```

JS 中分两种任务类型：`macrotask`（宏任务） 和 `microtask`（微任务），在 `ECMAScript` 中，`microtask` 称为 `jobs`，`macrotask` 可称为 `task`；

### 宏任务( `macrotask` )

每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）；

- 每一个宏任务会从头到尾将这个任务执行完毕，不会执行其它；
- 浏览器为了能够使得 JS 内部宏任务与 `DOM` 任务能够有序的执行，会在一个宏任务执行结束后，在下一个宏任务执行开始前，对页面进行重新渲染 （ 宏任务 => 渲染 => 宏任务 => ... ）；

### 微任务( `microtask` )

可以理解是在当前宏任务执行结束后立即执行的任务；

- 也就是说，在当前宏任务任务后，下一个宏任务之前，在渲染之前；
- 所以它的响应速度相比 `setTimeout`（因为 `setTimeout` 是宏任务）会更快，因为无需等渲染；
- 也就是说，在某一个宏任务执行完后，就会将在它执行期间产生的所有微任务都执行完毕（在渲染前）；

### 分别什么样的场景会形成宏任务和微任务呢？

- macrotask：
  - 主代码块；
  - `setTimeout`；
  - `setInterval` 等（可以看到，事件队列中的每一个事件都是一个宏任务）；
- microtask:
  - `process.nextTick`；
  - `Promise.then`；
  - `catch`；
  - `finally` 等；

> 补充：在 `node` 环境下，`process.nextTick` 的优先级高于 `Promise`，也就是可以简单理解为：在宏任务结束后会先执行微任务队列中的 `nextTickQueue` 部分，然后才会执行微任务中的 `Promise` 部分。

### 运行机制

以上概念弄明白之后，再来看事件循环机制是如何运行的呢？以下涉及到的任务执行顺序都是靠函数调用栈来实现的。

- 首先，事件循环机制的是从 `script` 标签内的代码开始的，上边我们提到过，整个 `script` 标签作为一个宏任务处理的；
- 在代码执行的过程中，如果遇到宏任务，如：`setTimeout`，就会将当前任务分发到对应的执行队列中去；
- 当执行过程中，如果遇到微任务，如：`Pomise`，在创建 `Promise` 实例对象时，代码顺序执行，如果到了执行 `·then` 操作，该任务就会被分发到微任务队列中去；
- `script` 标签内的代码执行完毕，同时执行过程中所涉及到的宏任务也和微任务也分配到相应的队列中去；
- 此时宏任务执行完毕，然后去微任务队列执行所有的存在的微任务；
- 微任务执行完毕，第一轮的消息循环执行完毕，页面进行一次渲染；
- 然后开始第二轮的消息循环，从宏任务队列中取出任务执行；
- 如果两个任务队列没有任务可执行了，此时所有任务执行完毕；

## 宏任务和微任务

上面也简单的介绍了宏任务和微任务的概念，下面我们主要是通过代码的方式来讲解，例题稍微有点多，请耐心看完~~~

**题目一**

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 1000);

new Promise((resolve, reject) => {
  console.log("3");
  resolve();
  console.log("4");
}).then(() => {
  console.log("5");
});

console.log("6");
复制代码
```

输出结果为：

```js
1
3
4
6
5
2
复制代码
```

- 初始化状态，执行栈为空；
- 首先执行 `<script>` 标签内的同步代码，此时全局的代码进入执行栈中，同步顺序执行代码，输出 1；
- 执行过程中遇到异步代码 `setTimeout`（宏任务），将其分配到宏任务异步队列中；
- 同步代码继续执行，遇到一个 `promise` 异步代码（微任务），但是构造函数中的代码为同步代码，依次输出3、4，则 `then` 之后的任务加入到微任务队列中去；
- 最后执行同步代码，输出 6；
- 因为 `script` 内的代码作为宏任务处理，所以此次循环进行到处理微任务队列中的所有异步任务，直达微任务队列中的所有任务执行完成为止，微任务队列中只有一个微任务，所以输出 5；
- 此时页面要进行一次页面渲染，渲染完成之后，进行下一次循环；
- 在宏任务队列中取出一个宏任务，也就是之前的 `setTimeout`，最后输出 2；
- 此时任务队列为空，执行栈中为空，整个程序执行完毕；

### 主线程上添加宏任务与微任务

执行顺序：主线程 => 主线程上创建的微任务 => 主线程上创建的宏任务

```js
console.log("start");

setTimeout(() => {
  console.log("setTimeout"); // 将回调代码放入另一个宏任务队列
}, 0);

new Promise((resolve, reject) => {
  for (let i = 0; i < 5; i++) {
    console.log(i);
  }
  resolve();
}).then(() => {
  console.log("Promise实例成功回调执行"); // 将回调代码放入微任务队列
});

console.log("end");
复制代码
```

输出结果为：

```js
start
0
1
2
3
4
5
end
Promise实例成功回调执行
setTimeout
复制代码
```

### 微任务中创建微任务

执行顺序：主线程 => 主线程上创建的微任务 => 微任务上创建的微任务 => 主线程上创建的宏任务

在微任务中包含微任务，执行时先执行微任务中包含的微任务，还是会排在主线程上创建出的宏任务之前执行。

```js
setTimeout(_ => console.log(4)); // 宏任务

new Promise(resolve => {
  resolve();
  console.log(1); // 同步
}).then(_ => {
  console.log(3); // 微任务
  Promise.resolve()
    .then(_ => {
      console.log("before timeout"); // 微任务中创建微任务，在宏任务之前执行
    })
    .then(_ => {
      Promise.resolve().then(_ => {
        console.log("also before timeout");
      });
    });
});

console.log(2);
复制代码
```

输出结果为：

```js
1
2
3
before timeout
also before timeout
4
复制代码
```

**同时有两个微任务执行：**

```js
setTimeout(_ => console.log(5)); // 宏任务

new Promise(resolve => {
  resolve();
  console.log(1); // 同步
})
  .then(_ => {
    console.log(3); // 微任务
    Promise.resolve()
      .then(_ => {
        console.log("before timeout"); // 微任务中创建微任务，在宏任务之前执行
      })
      .then(_ => {
        Promise.resolve().then(_ => {
          console.log("also before timeout");
        });
      });
  })
  .then(_ => {
    console.log(4); // 微任务
  });

console.log(2);
复制代码
```

输出结果为：

```js
1
2
3
before timeout
4
also before timeout
5
复制代码
setTimeout(_ => console.log(5)); // 宏任务

new Promise(resolve => {
  resolve();
  console.log(1); // 同步
})
  .then(_ => {
    console.log(3); // 微任务
    Promise.resolve()
      .then(_ => {
        console.log("before timeout1"); // 微任务中创建微任务，在宏任务之前执行
      })
      .then(_ => {
        Promise.resolve().then(_ => {
          console.log("also before timeout1");
        });
      });
  })
  .then(_ => {
    console.log(4); // 微任务
    Promise.resolve()
      .then(_ => {
        console.log("before timeout2"); // 微任务中创建微任务，在宏任务之前执行
      })
      .then(_ => {
        Promise.resolve().then(_ => {
          console.log("also before timeout2");
        });
      });
  });

console.log(2);
复制代码
```

输出结果为：

```js
1
2
3
before timeout1
4
before timeout2
also before timeout1
also before timeout2
复制代码
```

### 宏任务中创建微任务

执行顺序：主线程 => 主线程上的宏任务队列 => 宏任务队列中创建的微任务

在宏任务中创建微任务，在执行完当前宏任务后，就会去执行宏任务中包含的微任务。

```js
setTimeout(() => {
  console.log("timer_1");
  setTimeout(() => {
    console.log("timer_3");
  }, 500);
  new Promise(resolve => {
    resolve();
    console.log("new promise");
  }).then(() => {
    console.log("promise then");
  });
}, 500);

setTimeout(() => {
  console.log("timer_2");
}, 500);

console.log("end");
复制代码
```

输出结果为：

```js
end
timer_1
new promise
promise then
timer_2
timer_3
复制代码
```

### 微任务中创建的宏任务

执行顺序：主线程 => 主线程上创建的微任务 => 主线程上创建的宏任务 => 微任务中创建的宏任务

异步宏任务队列只有一个，当在微任务中创建一个宏任务之后，他会被追加到异步宏任务队列上（跟主线程创建的异步宏任务队列是同一个队列）

```js
new Promise(resolve => {
  console.log("new Promise(macro task 1)");
  resolve();
}).then(() => {
  console.log("micro task 1");
  setTimeout(() => {
    console.log("macro task 3");
  }, 500);
});

setTimeout(() => {
  console.log("macro task 2");
}, 1000);

console.log("Task queue");
复制代码
```

输出结果为：

```js
new Promise(macro task 1)
Task queue
micro task 1
macro task 3
macro task 2
复制代码
new Promise(resolve => {
  console.log("new Promise(macro task 1)");
  resolve();
}).then(() => {
  console.log("micro task 1");
  setTimeout(() => {
    console.log("macro task 3");
  }, 1000);
});

setTimeout(() => {
  console.log("macro task 2");
}, 1000);

console.log("Task queue");
复制代码
```

输出结果为：

```js
new Promise(macro task 1)
Task queue
micro task 1
macro task 2
macro task 3
复制代码
```

【注意】：如果把 `setTimeout(() => { // 宏任务2 console.log('macro task 2'); }, 1000)` 改为立即执行`setTimeout(() => { // 宏任务2 console.log('macro task 2'); }, 500)` 那么它会在 `macro task 3` 之前执行，**因为定时器是过多少毫秒之后才会加到事件队列里**。

**综合例子：**

```js
console.log("======== main task start ========");

new Promise(resolve => {
  console.log("create micro task 1");
  resolve();
}).then(() => {
  console.log("micro task 1 callback");
  setTimeout(() => {
    console.log("macro task 3 callback");
  }, 0);
});

console.log("create macro task 2");

setTimeout(() => {
  console.log("macro task 2 callback");
  new Promise(resolve => {
    console.log("create micro task 3");
    resolve();
  }).then(() => {
    console.log("micro task 3 callback");
  });
  console.log("create macro task 4");
  setTimeout(() => {
    console.log("macro task 4 callback");
  }, 0);
}, 0);

new Promise(resolve => {
  console.log("create micro task 2");
  resolve();
}).then(() => {
  console.log("micro task 2 callback");
});

console.log("======== main task end ========");
复制代码
```

输出结果为：

```js
======== main task start ========
create micro task 1
create macro task 2
create micro task 2
======== main task end ========
micro task 1 callback
micro task 2 callback
macro task 2 callback
create micro task 3
create macro task 4
micro task 3 callback
macro task 3 callback
macro task 4 callback
复制代码
```

### 包含 async/await

**题目一**

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end"); // await 语句后面的加入到微任务队列中
}
async function async2() {
  console.log("async2");
}

async1();

new Promise(resolve => {
  console.log("create micro task");
  resolve();
}).then(() => {
  console.log("micro task callback");
});

console.log("script start");
复制代码
```

输出结果为：

```js
async1 start
async2
create micro task
script start
async1 end
micro task callback
复制代码
```

**题目二**

```js
async function job1() {
  console.log("a");
  await job2();
  console.log("b"); // 添加到微任务队列
}

async function job2() {
  console.log("c");
}

setTimeout(function () {
  new Promise(function (resolve) {
    console.log("d");
    resolve();
  }).then(function () {
    console.log("e");
  });
  console.log("f");
});

job1();

new Promise(function (resolve) {
  resolve();
}).then(function () {
  console.log("g");
});

console.log("h");
复制代码
```

输出结果为：

```js
a
c
h
b
g
d 
f
e
复制代码
```

**题目三**

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2");
}

console.log("script start");

setTimeout(function () {
  console.log("setTimeout");
}, 0);

async1();

new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("promise2");
});

console.log("script end");
复制代码
```

输出结果为：

```js
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
复制代码
```

**题目四**

```js
async function t1() {
  console.log(1);
  console.log(2);
  new Promise(function (resolve) {
    console.log("promise3");
    resolve();
  }).then(function () {
    console.log("promise4"); // 微任务1
  });
  await new Promise(function (resolve) {
    console.log("b");
    resolve();
  }).then(function () {
    console.log("t1p"); // 微任务2
  });

  // await 语句后的加入微任务队列中1
  // 微任务5
  console.log(3);
  console.log(4);

  new Promise(function (resolve) {
    console.log("promise5");
    resolve();
  }).then(function () {
    console.log("promise6");
  });
}

setTimeout(function () {
  console.log("setTimeout");
}, 0);

async function t2() {
  console.log(5);
  console.log(6);
  await Promise.resolve().then(() => console.log("t2p")); // 微任务4

  // await 语句后的加入微任务队列中2
  // 微任务6
  console.log(7);
  console.log(8);
}

t1();

new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("promise2"); // 微任务3
});

t2();

console.log("end");
复制代码
```

输出结果为：

```js
1
2
promise3
b
promise1
5
6
end
promise4
t1p
promise2
t2p
3
4
promise5
7
8
promise6
setTimeout
复制代码
```

`await` 之后的代码必须等 `await` 语句执行完成后（包括微任务完成），才能执行后面的，也就是说，**只有运行完 `await` 语句，才把 `await` 语句后面的全部代码加入到微任务行列**，所以，在遇到 `await promise` 时，必须等 `await promise` 函数执行完毕才能对 `await` 语句后面的全部代码加入到微任务中。

所以，在等待 `await Promise.then` 微任务时：

- 运行其他同步代码；
- 等到同步代码运行完，开始运行 `await promise.then` 微任务；
- `await promise.then` 微任务完成后，把 `await` 语句后面的全部代码加入到微任务行列；

`await` 语句是同步的，`await` 语句后面全部代码才是异步的微任务。

**题目五**

```js
async function b() {
  console.log("b");
  await c();
  // 加入微任务队列2
  console.log("b1");
}

async function c() {
  console.log("c");
  await new Promise(function (resolve, reject) {
    console.log("promise1");
    resolve();
  }).then(() => {
    console.log("promise1-1"); // 微任务2
  });

  // 加入微任务队列1
  setTimeout(() => {
    console.log("settimeout1");
  });

  console.log("c1");
}

new Promise(function (resolve, reject) {
  console.log("promise2");
  resolve();
  console.log("promise2-1");
  reject();
})
  .then(() => {
    console.log("promise2-2"); // 微任务1

    setTimeout(() => {
      console.log("settimeout2");
      new Promise(function (resolve, reject) {
        resolve();
      }).then(function () {
        console.log("settimeout2-promise");
      });
    });
  })
  .catch(() => {
    console.log("promise-reject");
  });

b();

console.log("200");
复制代码
```

输出结果为：

```js
promise2
promise2-1
b
c
promise1
200
promise2-2
promise1-1
c1
b1
settimeout2
settimeout2-promise
settimeout1
复制代码
```

执行步骤：

- 从上自下执行，`new Promise` 函数立即执行 => 打印 ：`promise2`
- `resolve()` 将 `promise2-2` 放入**微**任务队列中
- `settimeout2` 放入**宏**任务队列
- 继续向下执行 => 打印 ：`promise2-1`
- 执行 `b()` 函数 => 打印 ：`b`
- 执行 `await c()` 函数 => 打印：`c`
- 进入 `await c()` 函数中 `new Promise` 函数 => 打印 ：`promise1`
- `resolve()` 将 `promise1-1` 放入**微**任务队列中
- `settimeout1` 放入**宏**任务队列
- 继续执行、无立即执行 => 打印 ：`200`
- 暂无执行任务，去**微**任务中执行第一个进入**微**任务的待执行动作 => 打印：`promise2-2`
- 继续执行**微**任务中序列中待执行动作 => 打印：`promise1-1`
- **微**任务中暂无执行动作，继续执行 `c()` 函数中的待执行代码 => 打印：`c1`
- `c()` 执行完毕，继续执行 `b()` 函数中待执行代码 => 打印：`b1`
- 至此，立即执行以**微**任务执行完毕，执行**宏**任务队列中第一个进入的待执行任务 => 打印 ：`settimeout2`
- **宏**任务一中，执行 `new Promise` 函数 `resolve()` 将 `settimeout2-promise` 放入**微**任务中，立即执行**微**任务 => 打印：`settimeout2-promise`
- 继续执行**宏**任务中待执行动作 => 打印：`settimeout1`

### 总结：

- 微任务队列优先于宏任务队列执行；
- 微任务队列上创建的宏任务会被后添加到当前宏任务队列的尾端；
- 微任务队列中创建的微任务会被添加到微任务队列的尾端；
- 只要微任务队列中还有任务，宏任务队列就只会等待微任务队列执行完毕后再执行；
- 只有运行完 `await` 语句，才把 `await` 语句后面的全部代码加入到微任务行列；
- 在遇到 ```await```  ```promise``` 时，必须等```await```  ```promise``` 函数执行完毕才能对  ```await``` 语句后面的全部代码加入到微任务中；
  - 在等待 ```await Promise.then``` 微任务时：
    - 运行其他同步代码；
    - 等到同步代码运行完，开始运行 `await promise.then` 微任务；
    - `await promise.then` 微任务完成后，把 `await` 语句后面的全部代码加入到微任务行列；