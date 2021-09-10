## JavaScript的cookie对象

通过document.cookie属性来创建、读取、删除cookie，cookie中的参数名称和值是必需的

```js
document.cookie = "name=Nicholas;expires=Thu, 01 Jan 1970 00:00:00 GMT";
```

## Storage类型

用于保存名/值对数据，只能存储字符串类型，增加了一下方法：clear()    getItem(name)  key(index)   removeItem(name)   setItem(name,value)

### 1、sessionStorage对象

sessionStorage是sotrage的实例，可以使用storage类型的方法，只存储会话数据，当浏览器关闭时sessionStorage失效  。不同页面无法共享sessionStorage的信息

### 2、localStorage对象

跨会话持久存储数据的机制，数据不会因为页面刷新，关闭窗口、标签页或者重启浏览器而丢失。相同浏览器的不同页面可以共享相同的localStorage

```js
// 使用方法存储数据
localStorage.setItem("name", "Nicholas");
// 使用属性存储数据
localStorage.book = "Professional JavaScript";
// 使用方法取得数据
let name = localStorage.getItem("name");
// 使用属性取得数据
let book = localStorage.book; 
```

