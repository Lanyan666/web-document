### 1、安装nodejs（npm）

### 2、安装webpack，webpack-cli

我是局部安装的，也就是说在项目中安装

### 3、package.json配置文件

打开命令行，进入新建项目，使用`npm init -y` 来生成一个package.json配置文件，在scripts对象下，自定义属性，下次就可以直接使用npm run + dev(srcipts中自定义的属性), 来打包项目了

![image-20210817221411439](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210817221411439.png)

### 4、webpack.config.js文件

配置入口文件和打包路径

entry入口属性中可以配置多个文件的入口，在output对象的filename属性中，通过"[name]" 来区分不同的入口文件名。

最后  运行npm run dev  就会生成打包文件到dist目录下， 这样就可以在文件中使用打包好的文件啦

![image-20210817221710602](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210817221710602.png)

![image-20210817221935978](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210817221935978.png)

![image-20210817221951300](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210817221951300.png)

### 5、babel-loader @babel/core @babel/preset-env

由于文件中使用到**es6**语法，低版本的浏览器不能解析，所以需要**Babel**，将es6语法部分转化为低版本浏览器也能解析的语法

安装Babel

```html
npm install -D babel-loader @babel/core @babel/preset-env webpack
```

**@babel/preset-env**包含很多插件，提供语法转化的规则， 包括箭头函数 类class

**@babel/core**是babel的核心库，所有的核心Api都在这个库里，这些Api供babel-loader调用

**babel-loader** 作为中间桥梁，通过调用babel/core的api来告诉webpack要如何处理js

```js
const path = require('path');
module.exports = {
    // entry: "./src/index.js",
    entry: {
        index: './src/index.js',
        hello: './src/hello.js'
    },
    output:{
        path:path.resolve(__dirname, 'dist'),
        filename: "[name].bundle.js"
    },
    module: {
        rules: [
          {
            test: /\.m?js$/,
            exclude: /(node_modules|bower_components)/,
            use: {
              loader: 'babel-loader',
              options: {
                presets: ['@babel/preset-env']
              }
            }
          }
        ]
      },
    mode: 'development'
}
```

之后运行项目重新打包，就可以将箭头函数转为转为正常函数了

![image-20210817223851410](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210817223851410.png)

![image-20210817223826546](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210817223826546.png)

##### 6、`@babel/polyfill` 

**它实际只是简单的把core-js和regenerator runtime包装了下，这两个包才是真正的实现代码所在**

babel 只负责语法转换，比如将 ES6 的语法转换成 ES5。但如果有些对象、方法，浏览器本身不支持，比如：

全局对象：Promise、WeakMap、Set、Map、Proxy 等。

全局静态函数：Array.from、Object.assign 等。

实例方法：比如 Array.prototype.includes 等。

此时，需要引入 **babel-polyfill** 来模拟实现这些对象、方法   通过内置的core-js

安装  

```js
npm install --save @babel/polyfill
//Because this is a polyfill (which will run before your source code), we need it to be a `dependency`, not a `devDependency`
```

在webpack.config.js中配置

```js
module.exports = {
  entry: ["@babel/polyfill", "./app/js"],
};
```

除了在webpack.config.js中的entry引入@babel/polyfill之外，在需要的src/index.js等js文件最顶部引入import '@babel/polyfill'也是一样的

引入babel-polyfill会有一定副作用，比如：

 		引入了新的全局对象，Promise，WeakMap等

 		修改现有的全局对象，比如修改了Array String的原型链等

这就是**babel-runtime**存在的理由，使用局部

```
npm install --save @babel/runtime
```

### 7、使用loader来处理css文件

安装

```
npm install --save-dev css-loader
npm install --save-dev style-loader
```

Then add the plugin to your `webpack` config. For example:

**file.js**

```js
import css from "file.css";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

### 8、压缩图片

安装  https://github.com/tcoopman/image-webpack-loader

```
npm install image-webpack-loader --save-dev
```

webpack.config.js

```js
rules: [{
  test: /\.(gif|png|jpe?g|svg)$/i,
  use: [
    'file-loader',
    {
      loader: 'image-webpack-loader',
      options: {
        bypassOnDebug: true, // webpack@1.x
        disable: true, // webpack@2.x and newer
      },
    },
  ],
}]
```

