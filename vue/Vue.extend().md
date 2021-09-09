# Vue.extend()

1、Vue.extend()获得的是一个构造函数，可以通过实例化生成一个vue实例

2、实例化时可以像这个实例传递参数，不过props的值需要通过propsData属性来传递

3、最后通过$mount()挂载到dom元素中

**简单版**----**自定义无参数标签**

```html
我们想象一个需求，需求是这样的，要在博客页面多处显示作者的网名，并在网名上直接有链接地址。我们希望在html中只需要写<message></message>   ，这和自定义组件很像，但是他没有传递任何参数，只是个静态标签。
```

```html
  <div id="app">
        <message></message>
    </div>
    <script>
        var extendDemo = Vue.extend({
            template: `<span>{{name}}</span>`,
            data(){
                return{
                    name:"lanyan"
                }
            }
        })
        new extendDemo().$mount('message')
    </script>
```

**稍复杂版**

```html
 <div id="app">
        <!-- <message></message> -->
        <div id="message"></div>
    </div>
    <script>
        // vue实例
        let vue = new Vue({
            el: '#app',
        });
        var extendDemo = Vue.extend({
            template: `<div><span>{{name}}</span>
            <div>{{title}}</div>
            <div @click="cancel">点击取消</div></div>`,
            data() {
                return {
                    name: "lanyan"
                }
            },
            props: {
                title: String,
                close: {
                    type: Function,
                    default: () => { }
                }
            },
            methods: {
                cancel() {
                    console.log("取消成功！");
                    this.close();
                }
            }
        })
        const ele = new extendDemo({
            propsData: {
                title: '大标题',
                close: function () {
                    ele.$el.remove()
                }

            }
        }).$mount('#message')
```



  <div id="app">
        <message></message>
    </div>
    <script>
        var extendDemo = Vue.extend({
            template: `<span>{{name}}</span>`,
            data(){
                return{
                    name:"lanyan"
                }
            }
        })
        new extendDemo().$mount('message')
    </script>