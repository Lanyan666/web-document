# CSS之动画实现



## 1、Transition(过渡)

### 1.1 @keyframes规则

用来创建动画

语法：`@keyframes animationname {keyframes-selector {css-styles;}}`

**animation-name** ： 定义动画的名称

**keyframes-selector：**动画时长的百分比

***css-styles***：css样式属性

```css
 @keyframes myMove {
            from {
                background-color: pink;
                left: 0px;
                top: 0px;
            }

            25% {
                background-color: rgb(224, 105, 125);
                left: 200px;
                top: 0px;
            }

            50% {
                background-color: rgb(187, 31, 57);
                left: 200px;
                top: 200px;
            }

            75% {
                background-color: rgb(92, 8, 22);
                left: 200px;
                bottom: 400px;
            }

            to {
                background-color: black;
                left: 200px;
                bottom: 600px;
            }
        }
```

### 1.2 animation-duration  

定义动画的执行时间

```css
.blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            animation-name: myMove;
            animation-duration: 5s;
        }
```

### 1.3 animation-delay

动画执行的延迟时间

```css
.blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            animation-name: myMove;
            animation-duration: 5s;
            animation-delay: 2s;
        }
```

### 1.4 animation-iteration-count

动画执行的次数   如果定义为inifite 表示会一直执行下去

```css
 .blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            animation-name: myMove;
            animation-duration: 5s;
            animation-iteration-count: 3;
        }
```

### 1.5 animation-direction

反向或交替动画  

- normal - 动画正常播放（向前）。默认值
- reverse - 动画以反方向播放（向后）
- alternate - 动画先向前播放，然后向后
- alternate-reverse - 动画先向后播放，然后向前

```
.blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            animation-name: myMove;
            animation-duration: 5s;
            animation-direction: alternate;
        }
```

### 1.6 animation-timing-function

指定动画的速度曲线

- ease - 指定从慢速开始，然后加快，然后缓慢结束的动画（默认）
- linear - 规定从开始到结束的速度相同的动画
- ease-in - 规定慢速开始的动画
- ease-out - 规定慢速结束的动画
- ease-in-out - 指定开始和结束较慢的动画
- cubic-bezier(n,n,n,n) - 运行您在三次贝塞尔函数中定义自己的值

```css
 .blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            animation-name: myMove;
            animation-duration: 5s;
            animation-timing-function: ease-out;
        }
```

### 1.7 animation-fill-mode

在动画开始之前、结束之后   或两者都结束时，规定目标元素的样式

- none - 默认值。动画在执行之前或之后不会对元素应用任何样式。
- forwards - 元素将保留由最后一个关键帧设置的样式值（依赖 animation-direction 和 animation-iteration-count）。
- backwards - 元素将获取由第一个关键帧设置的样式值（取决于 animation-direction），并在动画延迟期间保留该值。
- both - 动画会同时遵循向前和向后的规则，从而在两个方向上扩展动画属性

```css
.blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            background-color: plum;
            animation-name: myMove;
            animation-duration: 5s;
            animation-timing-function: linear;
            animation-fill-mode: forwards;
        }
```

### 1.8 animation-play-state

规定动画正在运行还是暂停  **paused | running**

```css
        .blackpink {
            position: relative;
            width: 200px;
            height: 100px;
            background-color: plum;
            animation-name: myMove;
            animation-duration: 5s;
            animation-timing-function: linear;
            animation-fill-mode: forwards;
            animation-play-state: paused;
        }
```

### 1.9简写形式

```css
div {
  animation-name: example;
  animation-duration: 5s;
  animation-timing-function: linear;
  animation-delay: 2s;
  animation-iteration-count: infinite;
  animation-direction: alternate;
}
```

```css
div {
  animation: example 5s linear 2s infinite alternate;
}
```



## 2、transition、transform、translate的联系与区别

### transition

样式过渡，即动画效果

### transform

对元素静态样式的转化，包括

```css
transform: translate(0px); //移动
tranform: rotate(10deg); //旋转
tranform: scale(2,1); //缩放
tranform: skew(45deg); //扭曲
```

### translate

top:50%; left:50%;

![image-20210813213018603](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210813213018603.png)

移动，是transform的一种样式属性, translate（-50%，-50%）是指：往上（x轴），往左（y轴）移动自身元素长宽的50%。

加上transform:translate（-50%，-50%）之后  就把元素往左和往上移动了自身元素的50%

![image-20210813213207181](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210813213207181.png)

