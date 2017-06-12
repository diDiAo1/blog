# 登录操作中`async await`的一些理解
## 1、`login.vue`中的登录方法
```
login() {
      this.$store.dispatch('loginSystem', { password: this.userMes.password, loginName: this.userMes.userName })
        .then((res) => {
            console.log('login.vue login() then()')
            this.savecookie('userName', this.userMes.userName, 1)
            this.$router.push('/index/1/管理控制台/' + this.userMes.userName)
        }, (err) => {
          console.warn(err)
        })
        console.log('login.vue login()')
    }
```
## 2、`vuex`中关于`login`的`actions`和`mutations`和方法
```
...
const actions = {
  async loginSystem ({ commit }, options) {
    console.log('login actions begin')
    let loginData
    try {
      loginData = await api.basicPlatform_login(options)
      commit(types.LOGIN, loginData)
      console.log('login actions 1111')
      // return loginData
    } catch (err) {
    }
    console.log('login actions 2222')
  }
}
const mutations = {
  [types.LOGIN] (state, data) {
    state.user = data.user
  }
}
...
```
## 3、总结
首先输出`log`顺序：
```
login actions begin
login.vue login()
login actions 1111
login actions 2222
login.vue login() then()
```
按顺序调用`loginSystem()`方法，先输出`login actions begin`，走到`loginData = await api.basicPlatform_login(options)`表示目前需要一段时间稍后执行，
此时这个方法里后面的代码都会等待，然后跳出这个方法，输出`login.vue login()`，等到`api.basicPlatform_login(options)`返回结果，才会依次输出
`login actions 1111   login actions 2222   login.vue login() then()`

若`loginData = await api.basicPlatform_login(options)`不加`await`,此时`api.basicPlatform_login(options)`是一个异步方法，并不会等待`data`有值后才做commit操作，
此时`commit`操作的`loginData`就是空值，尽管稍后`api.basicPlatform_login(options)`会走到
