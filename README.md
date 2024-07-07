

Object.defineProperty() 

Vue2 响应式的问题

Object.defineProperty() 可以监听对象的属性变化，但是有两个问题：
- 不能监听数组的变化，所以 Vue2 采用了 hack 的方式，重写了数组的方法，使得数组的方法能够触发更新。
- 不能监听对象新增的属性，所以 Vue2 提供了 Vue.set() 方法和 vm.$set() 方法，用于给对象添加响应式属性。

Vue3 使用 Proxy 代替了 Object.defineProperty()，Proxy 可以监听对象的属性变化，也可以监听数组的变化，也可以监听对象新增的属性。


代理模式
Proxy
Reflect

为什么需要 Reflect？

Reflect 是一个内置的对象，它提供了一系列静态方法，这些方法与 Proxy 的方法是一一对应的。Reflect 的方法和 Proxy 的方法是一一对应的，这样设计的目的是为了让 Proxy 的方法更易于理解。

当访问一个对象的属性时，如果这个属性不存在，会返回 undefined，这样会导致代码出现错误。Reflect 可以让我们更好地处理这种情况。

- 处理 this 的问题，当在代理对象内使用 this 访问自身属性。

```js
const target = {
  name: 'Tom',
  age: 18,
  say() {
    console.log(this.name)
  }
}

const proxy = new Proxy(target, {
  get(target, key, receiver) {
    console.log('get', key)
    return Reflect.get(target, key, receiver) // 不能简单的返回 target[key]，因为这样会导致 this 的问题
  }
})

roxy.say() // Tom, 会触发两次 get 方法，第一次是获取 say 方法，第二次是获取 name 属性
```

