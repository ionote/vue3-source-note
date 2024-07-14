# Vue3 数据劫持: Proxy

## Vue2 响应式的问题

Vue2 中响应式可以监听对象的属性变化，但是由于 Object.defineProperty() 限制，存在有两个问题：

- 不能监听数组的变化，所以 Vue2 采用了 hack 的方式，重写了数组的方法，使得数组的方法能够触发更新。
- 不能监听对象新增的属性，所以 Vue2 提供了 Vue.set() 方法和 vm.$set() 方法，用于给对象添加响应式属性。

Vue3 使用 Proxy 代替了 Object.defineProperty()，Proxy 可以监听对象的属性变化，也可以监听数组的变化，也可以监听对象新增的属性。

## Proxy

Proxy 是 ECMAScript 6 (ES6) 引入的一个强大的元编程特性,它允许你创建一个对象的代理,从而可以拦截并自定义该对象的基本操作。

Proxy 的基本语法如下:

```javascript
let proxy = new Proxy(target, handler)
```

- `target`: 需要被代理的对象
- `handler`: 一个对象,定义了代理行为

`handler` 对象中可以定义各种代理行为,常见的有:

1. `get(target, property, receiver)`: 拦截对象属性的读取
2. `set(target, property, value, receiver)`: 拦截对象属性的设置
3. `has(target, property)`: 拦截 `in` 操作符
4. `deleteProperty(target, property)`: 拦截 `delete` 操作符
5. `apply(target, thisArg, argumentsList)`: 拦截函数调用
6. `construct(target, argumentsList, newTarget)`: 拦截 `new` 操作符

下面是一个简单的例子,演示如何使用 Proxy 来拦截对象属性的读取和设置:

```javascript
let person = {
  name: 'Alice',
  age: 30
}

let personProxy = new Proxy(person, {
  get: function(target, property, receiver) {
    console.log(`Getting ${property}`)
    return target[property]
  },
  set: function(target, property, value, receiver) {
    console.log(`Setting ${property} to ${value}`)
    target[property] = value
    return true
  }
})

console.log(personProxy.name) // 输出: Getting name, Alice
personProxy.age = 31 // 输出: Setting age to 31
```

在这个例子中,我们创建了一个 `personProxy` 对象,它是 `person` 对象的代理。我们定义了 `get` 和 `set` 处理器,分别拦截对象属性的读取和设置操作,并在控制台输出相应的信息。

## Reflect

Reflect 是一个内置的对象，它提供了一系列静态方法，这些方法与 Proxy 的方法是一一对应的。Reflect 的方法和 Proxy 的方法是一一对应的，这样设计的目的是为了让 Proxy 的方法更易于理解。

当代理一个对象，而这个对象内部使用 this 访问自身属性时会存在 this 指向问题。

```js
const target = {
  lastName: '张',
  firstName: '三',
  get fullName() {
    return this.lastName + this.firstName
  },
}

const proxy = new Proxy(target, {
  get(target, key) {
    console.log('get', key)
    return target[key]
  }
})

console.log('fullName: ', proxy.fullName)

// 输出
// get fullName
// fullName: 张三
```

我们期望上面代码输出三次 get，但实际上只输出了一次。这是因为 Proxy 代理对象的时候，内部的 this 指向了 target 对象，而不是 proxy 对象。

为了解决这个问题，我们可以使用 Reflect 来代替 target 对象。

```js
const proxy = new Proxy(target, {
  get(target, key, receiver) {
    console.log('get', key)
    return Reflect.get(target, key, receiver) // receiver 参数会修改函数的 this 指向
  }
})

console.log('fullName: ', proxy.fullName)
// 输出
// get fullName
// get lastName
// get firstName
// fullName: 张三
```