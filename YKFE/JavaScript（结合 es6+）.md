## 缩进
缩进使用2个空格。

## 分号
鉴于目前所有代码都会经过构建工具进行构建，所以为了代码整洁性以及可读性，除特殊情况外，不需要加分号。

```js
// bad
const a = 1
const b = { obj: 'this is obj' }

// good
const a = 1
const b = { obj: 'this is obj' }

// 以下情况需要在括号前加分号，否则报错
const a = ['x', 'y', 'z']
;(a || []).forEach((item) => {
  console.log(item)
})
```

## 空格
以下几种情况需要空格：
- 对象`{`前；
- 对象的属性名和属性值前；
- 单行注释`//`后（若单行注释和代码同行，则`//`前也需要），多行注释`*`后。

## 单行注释
- 双斜线后，必须跟一个空格；
- 缩进与下一行代码保持一致；
- 可位于一个代码行的末尾，与代码间隔一个空格。

```js
if (condition) {
  // 双斜线后，必须跟一个空格，缩进与下一行代码保持一致
  allowed()
}

const zhangsan = 'zhangsan' // 可位于代码行的末尾，与代码间隔一个空格


/**
 * 接口请求或返回的字段较多时需要注释说明字段的含义，当前页面相同的字段至少需注释一次
 */
const form = {
  productType: undefined, 	// 煤种
  productName: undefined, 	// 品种
  productPlace: undefined, 	// 产地
  lowHot: undefined, 		// 低位热值
  sdljf: undefined, 		// 收到基硫分
  ...
}
```

## 多行注释
建议在以下情况下使用：
- 难于理解的代码段
- 可能存在错误的代码段
- 浏览器特殊的HACK代码
- 业务逻辑强相关的代码

```js
/**
 * 计算数字相加
 * @param {Number} a 参数1
 * @param {Number} b 参数2
 * @return {Number} c 返回值
 */
function add(a, b) {
  return a + b
}
```

## 引号
- 统一使用单引号；
- 当需要字符串拼接或模板字符串时使用反引号。

```js
const x = 'test' // 声明一个变量 `x`

const msg = 'please say "Hello World!"' // 字符串拼接

const msg = `please say 'Hello World'` // 字符串拼接

const y = 'test'
const msg = `this is ${ y }` // 模板字符串
```

## 变量命名
- 标准变量使用驼峰命名；
- 类名首字母需要大写；
- 常量采用大写+下划线；
- 对象属性名可用驼峰和小写+下划线两种方式，推荐使用驼峰。

```js
const myVar = 'test' // 标准变量

// 类名首字母大写
class MyCls {
 ...
}

// 也是类名，首字母需要大写
function MyCls {
  ...
}
new MyCls()

const MAX_SIZE = 1024 * 300 // 常量采用大写+下划线

const obj = {
  propFirst: 123, // 属性名驼峰写法，推荐
  'prop_second': 456 // 属性名可采用小写+下划线
}
```

## var、let、const
- let 完全取代 var。因为两者语义相同，而且let没有副作用，不存在变量提示效用；
- 如果变量不需要在未来改变，使用 const 来声明

## 函数
- 参数之间用 ', ' 分隔，注意逗号后有一个空格；
- 箭头函数参数即使只有一个也必须加上括号；
- 立即执行函数建议写成箭头函数的形式；
- 那些使用匿名函数当作参数的场合，尽量用箭头函数代替。因为这样更简洁，而且绑定了 this；
- 箭头函数取代Function.prototype.bind，不应再用 self、_this、that 绑定 this；
- 简单的、单行的、不会复用的函数，建议采用箭头函数。如果函数体较为复杂，行数较多，还是应该采用传统的函数写法；
- 所有配置项都应该集中在一个对象，放在最后一个参数，布尔值不可以直接作为参数；
- 不要在函数体内使用 arguments 变量，使用 rest 运算符（...）代替。因为 rest 运算符显式表明你想要获取参数，而且 arguments 是一个类似数组的对象，而 rest 运算符可以提供一个真正的数组；
- 使用默认值语法设置函数参数的默认值。

```js
// 1. 立即执行函数建议写成箭头函数的形式
(() => {
  // ...
})()


// 2. 匿名函数建议使用箭头函数
// bad
[1, 2, 3].map(function (x) {
  return x * x
})


// good
[1, 2, 3].map((x) => x * x)


// 3. 箭头函数取代Function.prototype.bind，不应再用 self、_this、that 绑定 this
// bad
const self = this;
const boundMethod = function(...params) {
  return method.apply(self, params)
}

// acceptable
const boundMethod = method.bind(this)

// best
const boundMethod = (...params) => {
  return method.apply(this, params)
}


// 4. 所有配置项都应该集中在一个对象，放在最后一个参数，布尔值不可以直接作为参数
// bad
function divide(a, b, option = false ) {
  // ...
}

// good
function divide(a, b, { option = false } = {}) {
  // ...
}


// 5. 不要在函数体内使用 arguments 变量，使用 rest 运算符（...）代替
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments)
  return args.join('')
}

// good
function concatenateAll(...args) {
  return args.join('')
}


// 6. 使用默认值语法设置函数参数的默认值
// bad
function handleThings(opts) {
  opts = opts || {}
}

// good
function handleThings(opts = {}) {
  // ...
}
```

## 对象
- 对象属性名不需要加引号；
- 对象最后不要有逗号；
- 对象的属性和方法，尽量采用简洁表达法，这样易于描述和书写。

```js
// bad
const obj = { x: 123, y: 456, }

// good
const obj = { x: 123, y: 456 }

// bad
const ref = 'some value'
const atom = {
  ref: ref,
  value: 1,
  addValue: function (value) {
    return this.value + value
  }
}

// good
const ref = 'some value'
const atom = {
  ref,
  value: 1,
  addValue (value) {
    return this.value + value
  }
}
```

## 数组
- 数组最后不要有逗号；
- 使用扩展运算符（...）拷贝数组。

```js
// bad
const arr = ['x', 'y', 'z',]

// good
const arr = ['x', 'y', 'z']

// bad
const len = items.length
const itemsCopy = []
let i
for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i]
}

// good
const itemsCopy = [...items]
```

## 解构赋值
- 使用数组成员对变量赋值时，优先使用解构赋值；
- 函数的参数如果是对象的成员，优先使用解构赋值；
- 如果函数返回多个值，优先使用对象的解构赋值，而不是数组的解构赋值。这样便于以后添加返回值，以及更改返回值的顺序。

```js
const arr = [1, 2, 3, 4]

// bad
const first = arr[0]
const second = arr[1]

// good
const [first, second] = arr

// bad
function getFullName (user) {
  const firstName = user.firstName
  const lastName = user.lastName
}

// good
function getFullName ({ firstName, lastName }) {
  // ...
}

// bad
function processInput (input) {
  return [left, top, right, bottom]
}

// good
function processInput (input) {
  return { left, top, right, bottom }
}
```

## Map 结构
- 注意区分 Object 和 Map，只有模拟现实世界的实体对象时，才使用 Object。如果只是需要 key: value 的数据结构，使用 Map 结构。因为 Map 有内建的遍历机制。

```js
let map = new Map(arr)

for (let key of map.keys()) {
  console.log(key)
}

for (let value of map.values()) {
  console.log(value)
}

for (let item of map.entries()) {
  console.log(item[0], item[1])
}
```

## 模块
- 如果模块只有一个输出值，就使用 export default，如果模块有多个输出值，就不使用 export default，export default 与普通的 export 不要同时使用。

## Vue 相关
- 调用公共资源时路径统一使用别名；
- 给每个组件添加name属性，属性名不能重复且首字母大写驼峰命名；
- props属性声明时采用驼峰命名，调用时使用中划线；
- components属性声明时采用首字母大写驼峰命名，调用时使用中划线；
- 自定义事件使用中划线；
- ref 属性使用驼峰命名；
- 路由地址使用中划线。

```js
// bad
import ProductAdd from '../../components/upload'

// good
import ProductAdd from '@/components/product/add'

export default {
  name: 'ProductAdd'
}

Vue.component('blog-post', {
// 在 JavaScript 中是 camelCase 的
 props: ['postTitle'],
 template: '<h3>{{ postTitle }}</h3>'
})
// 在 HTML 中是 kebab-case 的
<blog-post post-title="hello!"></blog-post>

// 在 HTML 中是 kebab-case 的
<product-add></product-add>
 ...
import ProductAdd from '@/views/product/add'
exports default {
 components: {
  // 在 JavaScript 中是 camelCase 的,首字母大写
    ProductAdd
  }
}


// 监听事件
<my-component v-on:my-event=“doSomething"/>
// 触发事件
this.$emit('my-event')


// ref 使用驼峰命名
<base-input ref="usernameInput"/>


// 路由使用中划线
const router {
  path: '/helper-center',
  component: ProductAdd,
  ...
}
```