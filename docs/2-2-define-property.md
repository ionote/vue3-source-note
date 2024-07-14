# Vue2 数据劫持: Object.defineProperty

在 Vue2 中，Vue.js 使用 Object.defineProperty 来实现数据劫持。Object.defineProperty 是 ES5 中的一个方法，用于直接在对象上定义一个新属性，或者修改现有属性，并返回该对象。通过这个方法，可以精确地控制属性的行为（如可写性、可枚举性和可配置性），以及定义属性的getter和setter函数。

## 语法
```javascript
Object.defineProperty(obj, prop, descriptor)
```

- `obj`：要在其上定义属性的对象。
- `prop`：要定义或修改的属性的名称。
- `descriptor`：属性描述符对象，用于描述属性的特性。

## 属性描述符
属性描述符有两种主要类型：数据描述符和存取描述符。两者之间不能混用。

### 数据描述符（Data Descriptor）
包含一个值和三个可选的属性特性：

- `value`：属性的值，默认值为`undefined`。
- `writable`：一个布尔值，表示属性的值是否可以被修改，默认值为`false`。
- `enumerable`：一个布尔值，表示属性是否会出现在对象的枚举属性中，默认值为`false`。
- `configurable`：一个布尔值，表示属性描述符是否可以被改变，以及属性是否可以被删除，默认值为`false`。

### 存取描述符（Accessor Descriptor）
包含一对getter和setter函数，以及两个可选的属性特性：

- `get`：一个函数，当属性被访问时调用。默认为`undefined`。
- `set`：一个函数，当属性值被修改时调用。默认为`undefined`。
- `enumerable`：同上。
- `configurable`：同上。

## 示例

### 定义一个简单的数据属性
```javascript
let obj = {};
Object.defineProperty(obj, 'a', {
  value: 37,
  writable: true,
  enumerable: true,
  configurable: true
});

console.log(obj.a); // 37
```

### 定义一个只读属性
```javascript
let obj = {};
Object.defineProperty(obj, 'b', {
  value: 42,
  writable: false
});

obj.b = 25; // 无效操作，属性b是只读的
console.log(obj.b); // 42
```

### 定义一个存取属性
```javascript
let obj = {};
let value = 0;

Object.defineProperty(obj, 'c', {
  get() {
    return value;
  },
  set(newValue) {
    value = newValue;
  },
  enumerable: true,
  configurable: true
});

obj.c = 100;
console.log(obj.c); // 100
```

## 使用场景
- **实现数据绑定**：如Vue.js中，通过`Object.defineProperty`实现对数据的劫持，从而实现响应式。
- **定义不可变属性**：通过设置`writable`和`configurable`为`false`，可以创建不可改变的常量属性。
- **隐藏内部实现**：通过使用getter和setter，可以控制属性的访问，隐藏内部实现细节。

## 注意事项
- 如果你在一个对象上定义一个属性，但不指定`writable`、`enumerable`和`configurable`，它们的默认值都是`false`。
- 使用`Object.defineProperty`添加的属性默认是不可枚举的（`enumerable: false`）。

总之，`Object.defineProperty`提供了对对象属性的细粒度控制，使开发者可以创建更灵活和强大的数据结构和API。
