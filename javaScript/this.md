## this

JavaScript的this总是指向一个对象，而具体指向哪个对象是基于函数的执行环境动态绑定的，而非函数被声明时的环境。

#### this的指向

除去with和eval的情况，this的指向大致分为四种

- 作为对象的方法调用
- 作为普通函数调用
- 构造器调用
- ```Function.prototype.call ``` 或  ```Function.prototype.apply``` 调用



##### 作为对象的方法调用

当函数作为对象的方法调用是，this指向该对象 

``` javascript
var obj = {
    a: 1,
    getA: function(){
		alert(this === obj) // true
        alert(this.a) // 1
    }
}

obj.getA()
```

##### 作为普通函数调用

当作为普通函数调用时，this总是指向全局对象。在浏览器中，这个全局对象就是window对象。

``` javascript
window.name = 'globalName'
var myObject = {
    name: 'sven',
    getName: function(){
		return this.name
    }
}

var getName = myObject.getName
getName() // globalName
```

##### 构造器调用

当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的this就指向返回的这个对象。

``` javascript
var MyClass = function(){
    this.name = 'sven'
}
var obj = new MyClass()
alert(obj.name) // sven
```

##### ```Function.prototype.call ``` 或  ```Function.prototype.apply``` 调用

跟普通的函数调用相比，能够动态改变传入函数的this

```javascript
var obj1 = {
	name: 'sven',
    getName: function(){
        return this.name
    }
}

var obj2 = {
	name: 'anne'
}

console.log(obj1.getName()) // sven
console.log(obj1.getName.call( obj2 )) // anne
```

