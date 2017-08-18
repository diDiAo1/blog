# vue项目中的小总结

标签（空格分隔）： vue

---

## 关于store
### **1、为啥我们项目中`store`的文件夹中会存在`mutations.type.js`**
`store` 基本上就是一个容器，它包含着你的应用中大部分的状态(`state`)。`Vuex` 和单纯的全局对象有以下两点不同：
 1. Vuex 的状态存储是响应式的。
 2. 你不能直接改变 store中的状态。改变 store 中的状态的唯一途径就是显式地提交(commit) mutations。
 `store`是包含许多`state`的容器，要改变`state`的值，只能通过显示地提交`commit mutations`，`mutations`是同步的。`action`类似`mutations`，但`action`提交的是`mutations`，自己没有直接修改`state`，`action`可以包含任意异步操作，所以`action`是异步的
mutations.type.js文件的作用如图：
![mutations_type.js][1]
`mutations.type.js`虽然在项目中不是必须存到的，主要作为action与mutation中间沟通的对应表，是当系统成长到一定的程度，防止mutation key重复，造成不可预期情况发生
亲测action中的方法名字倒是可以重复，你在.vue文件中`this.$store.dispatch('actionName')`,会触发两个action，若两个action都有返回值，则`this.$store.dispatch('actionName').then((res)=>{console.log(res)})`中的res是包含两个返回值的数组

### **2、浏览器刷新按钮点击或刷新浏览器地址store数据会清空，但是浏览器的前进和后退按钮以及this.$router控制的路由却不会清空数据**
vuex就是一个“提升变量”的一个工具，它是将state当做全局变量存储。刷新相当于App重载了，自然随着页面的刷新也会重新初始化state。
若你不想刷新后数据消失，可以放在cookie,localStorage等中，这里面的数据不手动清除缓存一般都是在的

## 自定义路径别名

 1. 在 vue-cli 生成的模板中在导入组件时使用了这样的语法：
 `import Index from '@/components/Index'`
 2. 我们也可以在基础配置文件中添加自己的路径别名，比如下面这个就把 ~ 设置为路径 src/components 的别名：
 ```
 // build/webpack.base.js
{
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      '~': resolve('src/components')
    }
  }
}
```
 3. 然后我们导入组件的时候就可以这样写：
 `import YourComponent from '~/YourComponent'`
 

  [1]: https://github.com/diDiAo1/blog/blob/master/images/vue/mutations_type.png
