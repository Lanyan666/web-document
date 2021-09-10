## JSON对象

json对象有两个方法：

### 1、stringify() 将JavaScript序列化为JSON字符串

JSON.stringify()方法可接收3个参数，JavaScript对象、函数、控制缩进和空格

```js
let jsonText = JSON.stringify(book, null, "--" );
这样，jsonText 的值会变成如下格式：
{
--"title": "Professional JavaScript",
--"authors": [
----"Nicholas C. Zakas",
----"Matt Frisbie"
--],
--"edition": 4,
--"year": 2017
} 
```



```js
let book = {
 title: "Professional JavaScript",
 authors: [
 "Nicholas C. Zakas",
 "Matt Frisbie"
 ],
 edition: 4,
 year: 2017
};
let jsonText = JSON.stringify(book); 

jsonText 的值是这样的：
{"title":"Professional JavaScript","authors":["Nicholas C. Zakas","Matt Frisbie"],
"edition":4,"year":2017} 
```

### 2、parse()  将JSON解析为原生JavaScript值

在序列化JavaScript对象时，所有函数和原型成员都会有意地 在结果中省略，此外，值为undefined的任何属性也会被跳过。

```js
let bookCopy = JSON.parse(jsonText);
```

