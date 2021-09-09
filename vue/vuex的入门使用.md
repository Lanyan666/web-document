# vuex是什么

采用**集中式存储管理**应用的所有组件的状态，借鉴了flux、redux

## 1、state

在store中定义数据  在组件中直接使用

目录： `store/index.js`

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        num: 0
    },
    getters: {},
    mutations: {},
    actions: {},
    modules: {}
})

```

目录：`Home.vue`

```html
<template>
  <div class="home">
    <h2>home页面的数字是：{{ num }}</h2>
  </div>
</template>
<script>
export default {
  computed: {
    num() {
      return this.$store.state.num;
    },
  },
};
</script>

```

目录：`About.vue`

```html
<template>
  <div class="home">
    <h2>about页面的数字是：{{ num }}</h2>
  </div>
</template>
<script>
export default {
  computed: {
    num() {
      return this.$store.state.num;
    },
  },
};
</script>

```

## 2、getters

相当于组件中的computed， getters是全局的

目录：store/`index.js`

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        num: 0
    },
    // 相当于组件中的computed  getters是全局的  
    getters: {
        getNum(state) {
            return state.num
        }
    },
    mutations: {},
    actions: {},
    modules: {}
})

```

目录：`Home.vue`

```html
<template>
  <div class="home">
    <h2>home页面的数字是：{{ $store.getters.getNum }}</h2>
  </div>
</template>
<script>
export default {};
</script>

```

## 3、mutations

改变vuex中的store中的状态**唯一**的方法是提交**mutation**。

store/`index.js`

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        num: 0
    },
    // 相当于组件中的computed  getters是全局的  
    getters: {
        getNum(state) {
            return state.num
        }
    },
    // 不能使用异步的方法  比如定时器、 axios
    mutations: {
        // payLoad是一个形参
        addNum(state, payLoad) {
            state.num += payLoad ? payLoad : 1;
        }
    },
    actions: {},
    modules: {}
})

```

`Btn.vue`

```vue
<template>
  <div class="btn">
    <button @click="addNum">click it</button>
  </div>
</template>
<script>
export default {
  methods: {
    addNum() {
      this.$store.commit("addNum", 5);
    },
  },
};
</script>
```

## 4、actions

action专门用来处理**异步**，但实际修改状态值的**依然是mutations**

store/`index.js`

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        num: 0
    },
    // 相当于组件中的computed  getters是全局的  
    getters: {
        getNum(state) {
            return state.num
        }
    },
    // 不能使用异步的方法  比如定时器 axios
    mutations: {
        // payLoad是一个形参
        addNum(state, payLoad) {
            state.num += payLoad ? payLoad : 1;
        },
        subNum(state){
            state.num--
        }
    },
    // 专门处理异步 实际修改状态值的  依然是mutations
    actions: {
        subNumAsync(context){
            context.commit('subNum')
        }
    },
    modules: {}
})

```

`btn.vue`

```vue
<template>
  <div class="btn">
    <button @click="addNum">click 加法</button>
    <button @click="subNum">click 减法</button>
  </div>
</template>
<script>
export default {
  methods: {
    addNum() {
      this.$store.commit("addNum", 5);
    },
    subNum() {
      this.$store.dispatch("subNumAsync");
    },
  },
};
</script>
```

## 5、辅助函数 map*

**map* 其实就是store文件中的一个映射，调用一个值的时候可以不用敲这么多代码**

### mapState

在computed属性中

```html
<template>
  <div class="home">
    <h2>home页面的数字是：{{ num }}</h2>
  </div>
</template>
<script>
import { mapState } from 'vuex'
export default {
    computed: mapState({
        num: state=>state.num
    })
};
</script>

```

当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 `mapState` 传一个字符串数组

```html
<template>
  <div class="home">
    <h2>home页面的数字是：{{ num }}</h2>
  </div>
</template>
<script>
import { mapState } from 'vuex'
export default {
    computed: mapState(['num'])
};
</script>

```

对象展开运算符

```html
<template>
  <div class="home">
    <h2>home页面的数字是：{{ num }}</h2>
  </div>
</template>
<script>
import { mapState } from "vuex";
export default {
  computed: {
    ...mapState(["num"]),
  },
};
</script>

```

### mapGetters

定义在computed属性中

`mapGetters` 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```html
<template>
  <div class="home">
    <h2>about页面的数字是：{{ getNum }}</h2>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters(["getNum"]),
  },
};
</script>

```

### mapMutations

### mapActions

都是在methods中

```html
<template>
  <div class="btn">
    <button @click="addNum()">click 加法</button>
    <button @click="subNumAsync()">click 减法</button>
  </div>
</template>
<script>
import { mapMutations, mapActions } from 'vuex'
export default {
  methods: {
    ...mapMutations(['addNum']),
    ...mapActions(['subNumAsync'])
  },
};
</script>
```

## 6、modules

将store再细分模块，每个模块单独拥有自己的 state、mutation、action、getter、甚至是嵌套子模块

![image-20210704001344171](C:\Users\lanyan\AppData\Roaming\Typora\typora-user-images\image-20210704001344171.png)

