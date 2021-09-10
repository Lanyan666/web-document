### 定义

函数作为一个参数传递到另一个函数(主函数)中，当主函数执行完后，再执行作为参数传进来的回调函数。

在大多数编程语言中，函数的形参总是由外到内向函数体传递参数，但在**JavaScript**中如果形参是关键字**callback**则完全相反，它表示函数体在完成某种操作后由内向外调用某个外部函数。

由于JavaScript语言是**异步单线程**语言，所以会出现排在最前面的代码不一定会比后面的代码先执行。会产生**异步执行的操作**有如下：

```html
1、定时器
2、建立网络连接
3、读取网络流数据
4、向文件写入数据
5、Ajax提交
6、请求数据库服务
```



### 例子

```js
var fs = require("fs");

function f(x) {
    console.log(x)
}

function writeFile(callback) { //关键字callback，表示这个参数不是一个普通变量，而是一个函数
    fs.writeFile('input.txt', '我是通过fs.writeFile 写入文件的内容', function (err) {
        if (!err) {
            console.log("文件写入完毕!")
            c = 1
            callback(c) // 因为我们传进来的函数名是f()，所以此行相当于调用一次f(c)
        }
    });
}
var c = 0
writeFile(f) // 函数f作为一个实参传进writeFile函数
```



### 缺点

尽管回调函数可以解决异步执行的问题，但如果回调有多层，会出现函数作为参数层层嵌套的回调地狱问题，这时候出现了`Promise，Async function`。