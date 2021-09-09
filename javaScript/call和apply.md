### call、apply和bind

#### call 和 apply

区别：传入参数形式不同

- apply接受两个参数，第一个参数指定函数体内this对象的指向，第二个参数为一个带下表的集合，这个集合可以为数组，也可以为类数组

  ```javascript
  var func = function(a,b,c){
      alert([a,b,c])
  }
  
  func.apply(null, [1,2,3]) // [1,2,3]
  ```

- call传入的参数数量不固定，与apply相同，第一个参数指定函数体内this对象的指向，从第二个参数开始往后，每个参数被依次传入函数

  ```javascript
  var func = function(a,b,c){
      alert([a,b,c])
  }
  
  func.call(null, 1,2,3) // [1,2,3]
  ```

当传入的第一个参数为null，函数体内的this会指向默认的宿主对象，在浏览器中则是window

```javascript
var func = function(a,b,c){
	alert(this === window)
}
func.apply(null, [1,2,3]) // true
```

#### call和apply的用途

- 改变this指向

  ```javascript
  var obj1 = {
  	name: 'sven'
  }
  var obj = {
      name: 'anne'
  }
  window.name = 'window'
  var getName = function(){
  	console.log(this.name)
  }
  
  getName() // window
  getName.call(obj1) // sven
  getName.call(obj2) // anne
  ```

- 借用其他对象的方法

  - 借用构造函数，实现类似继承的效果

    ``` javascript
    var A = function(name){
        this.name = name
    }
    var B = function(){
        A.apply(this, arguments)
    }
    B.prototype.getName = function(){
        return this.name
    }
    var b = new B('sven')
    console.log(b.getName()) // sven
    ```

  - 类数组对象使用数组方法

    ``` javascript
    (function(){
        Array.prototype.push.call(arguments, 3)
        console.log(arguments) // [1,2,3]
    })(1,2)
    ```

    