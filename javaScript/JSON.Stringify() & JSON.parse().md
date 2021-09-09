## 1、`JSON.Stringify()和JSON.parse()`

将JavaScript序列化为`JSON`字符串，以及将`JSON`解析为原生JavaScript值

在`JSON.stringify()`方法一共能接受3个参数，其中两个可选的参数（分别是第二、第三个参数）。这两个可选参数可以用于指定其他序列化JavaScript对象的方式。**第二个参数是过滤器**，可以是数组或函数；**第三个参数是用于缩进结果JSON字符串的选项**。单独或组合使用这些参数可以更好地控制JSON序列化。



## 2、实现对象深拷贝

```js
let arr1 = [1, 3, {
    username: ' kobe'
}];
let arr2 = JSON.parse(JSON.stringify(arr1));
arr2[2].username = 'duncan'; 
console.log(arr1, arr2)
```

![image-20210809224143574](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210809224143574.png)

这是利用`JSON.stringify`将对象转成JSON字符串，再用`JSON.parse`把字符串解析成对象，一去一来，新的对象产生了，新对象会开辟新的栈，实现深拷贝。

这种方法虽然可以实现数组或对象深拷贝,**但不能处理函数和正则**，因为这两者基于`JSON.stringify`和`JSON.parse`处理后，得到的正则就不再是正则（变为空对象），得到的函数就不再是函数（变为`null`）了。

### 注意事项

### 1、被转换值中有 `NaN` 和 Infinity

```js
let myObj = {
  name: "浪里行舟",
  age: Infinity,
  money: NaN,
};
console.log(JSON.stringify(myObj));
// {"name":"浪里行舟","age":null,"money":null}

JSON.stringify([NaN, Infinity])
// [null,null]
```

### 2、被转换值中有 undefined、任意的函数以及 symbol 值

分为两种情况：

1）数组,`undefined`、任意的函数以及`symbol`值在序列化的过程中会被转换成 `null`

```js
JSON.stringify([undefined, function () { }, Symbol("")]);
// '[null,null,null]'
```

2）非数组,`undefined`、任意的函数以及`symbol`值在序列化的过程中会被忽略

```js
JSON.stringify({ x: undefined, y: function () { }, z: Symbol("") });
// '{}'
```

### 3、循环引用

如果一个对象的属性值通过某种间接的方式指回该对象本身，那么就是一个循环引用。比如

```js
let bar = {
  a: {
    c: foo
  }
};
let foo = {
  b: bar
};

JSON.stringify(foo)
// 错误信息
Uncaught ReferenceError: foo is not defined
    at <anonymous>:3:8
```

### 4、含有不可枚举的属性值时

```js
let personObj = Object.create(null, {
  name: { value: "浪里行舟", enumerable: false },
  year: { value: "2021", enumerable: true },
})

console.log(JSON.stringify(personObj)) // {"year":"2021"}
```

