# Vue 知识点

##1.vue中methods、watch和computed的区别：

####1.vue中methods和(watch/computed)的对比:

三者的共同点，都是vue的属性，都是以函数为基础的；
不同之处：methods是定义函数，需要主动调用才能执行，而watch/computed相关的函数，会自动执行

####2.watch和computed的区别：

* watch:属于监听机制+事件机制:
    擅长处理的场景：一个数据影响多个数据（改变一个数据，其他数据都受到影响）
```js
watch: {
  firstName: function (val) { this.fullName = val + this.lastName }
}
实例: 验证码输入格式正确与否,影响按钮的状态和接口请求(一影响多)
监听到事件发生后执行的方法
```

* computed是计算属性,和data里的数据属性是一致的
    擅长处理的场景：一个数据受到多个数据影响
```js
computed:{
  e: function () { return this.firstName + lastName }
}
实例: 计算收益= 年化率*本金*时间 (一受多影响)
使用: 在取用的时候,用this.e,就和取data一样(不要当成函数调用!!)

conputed有个特点是缓存计算结果,就是依赖数据没有变化时,computed就不会再度进行计算.
只有符合: 1.存在依赖型数据 2.依赖型数据发生改变这两个条件,computed才会重新计算
new date()  一般不把它当成依赖型数据，data里的数据是依赖型数据。
```
computed是用来处理你使用watch和methods的时候无法处理,或者是处理起来并不太恰当的情况的


## 2.Vue.nextTick 的原理和用途
nextTick(() => {}) 的原理 在下次 DOM更新循环结束之后 执行延迟回调。
在修改数据之后立即使用它，然后等待 DOM 更新
![](nextTick.png)
[nextTick](https://segmentfault.com/a/1190000012861862)  相当于setTimeout(fn, 0) 也可以同样获取到。
                                                        
```js
同步代码执行 -> 查找异步队列，推入执行栈，执行Vue.nextTick[事件循环1] ->查找异步队列，推入执行栈，执行Vue.nextTick[事件循环2]...
总之，异步是单独的一个tick，不会和同步在一个 tick 里发生，也是 DOM 不会马上改变的原因。

1、修改数据
2、更新dom
3、执行某一块代码(和dom相关的)

created()钩子函数进行的DOM操作一定要放在Vue.nextTick()的回调函数中。因为created时期，dom并未进行任何渲染。
在 created 和 mounted 阶段，如果需要操作渲染后的视图，也要使用 nextTick 方法。
整个视图都渲染完毕，可以用 vm.$nextTick 。
```
```js
axios.get('/promotion/award-list/index', {params: {key: 'promo_180724'}})
    .then(response => {
        this.giftsList = response.data;
        this.isShowGiftsList = !this.isShowGiftsList;
        
        //奖品列表数据加载完之后，再走下面的延迟回调
        this.$nextTick(() => {
            let scroll = new BScroll('.wrapper');
        })
    })
    .catch(error => {
        try {
            Toast(error.response.data.message);
        } catch (error) {
            console.log(error)
        }
    });

运用场景： 有时候拿不到dom的属性，原因是在获取不到dom
点击按钮显示原本以 v-show = false 隐藏起来的输入框，并获取焦点。
使用 swiper 插件通过 ajax 请求图片后的滑动问题。
```

### 异步执行的运行机制如下：
```html
1) js是单线程，只有一个主线程栈，所有同步任务都在主线程上执行，形成一个执行栈（execution context stack 堆）
2) 主线程之外，还存在一个"任务队列"（task queue）
3) 所有同步任务执行完毕，系统就会读取"任务队列"，对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

```

##3.vue 自定义组件 v-model双向绑定、 父子组件同步通信


##4.Vue.set响应式新增与修改数据--动态控制数据的增减
调用方法：Vue.set( target, key, value )

target：要更改的数据源(可以是对象或者数组)

key：要更改的具体数据

value ：重新赋的值