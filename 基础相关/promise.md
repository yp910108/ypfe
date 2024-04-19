## 问题
- generator 中 it.next(value)？

#### 调用 resolve 或 reject 并不会终结 Promise 的参数函数的执行。
```js
new Promise((resolve, reject) => {
  resolve(1)
  console.log(2)
}).then(r => {
  console.log(r)
})
```
#### 如果 Promise 状态已经变成resolved，再抛出错误是无效的。
```js
const promise = new Promise((resolve, reject) => {
  resolve('ok')
  throw new Error('test') // 不会被捕获，等于没有抛出。因为 Promise 的状态一旦改变，就永久保持该状态，不会再变了。
})
promise
  .then((value) => {
    console.log(value)
  })
  .catch((error) => {
    console.log(error)
  })
```
#### 跟传统的 try/catch 代码块不同的是，如果没有使用 catch() 方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应。
```js
// 下面代码中，someAsyncThing()函数产生的 Promise 对象，
// 内部有语法错误。浏览器运行到这一行，会打印出错误提示ReferenceError: x is not defined，
// 但是不会退出进程、终止脚本执行，2 秒之后还是会输出123。
// 这就是说，Promise 内部的错误不会影响到 Promise 外部的代码，通俗的说法就是“Promise 会吃掉错误”。
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    resolve(x + 1)
  })
}

someAsyncThing().then((res) => {
  console.log(res)
})

setTimeout(() => {
  console.log(123)
}, 2000)

// 下面的输出结果是 0 报错
const promise = new Promise((resolve) => {
  console.log(x + 1) // 相当于 reject
  resolve(123)
})
console.log(0)
promise.then((res) => {
  console.log(res)
})
```

> 补充：因为 setTimeout 是下一轮“事件循环”再抛出错误，所以成了未捕获的错误。

```js
try {
  setTimeout(() => {
    throw new Error('xxx')
  })
} catch (e) {
  console.log(e)
}
```
#### 立即 resolve() 的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时
```js
// setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出
setTimeout(function () {
  console.log('three');
}, 0);
Promise.resolve().then(function () {
  console.log('two');
});
console.log('one');
```
#### generator 配合 thunk 自动执行
```js
function thunkify(fn, ...args) {
  return function(done) {
    fn.call(this, ...args, done)
  }
}
const readFile = function(filePath, callback) {
  console.log(`读取：${filePath}`)
  setTimeout(() => {
    callback(new Error('xxx'), `返回结果：${filePath}`)
  }, 2000)
}
const read = thunkify.bind(null, readFile)

function run(gen) {
  const it = gen()
  const next = (err, data) => {
    if (err) throw err
    const { value, done } = it.next(data)
    if (!done) value(next)
  }
  next()
}

function* getFiles() {
  const r1 = yield read('/ect/a.txt')
  console.log(r1.toString())
  const r2 = yield read('/etc/b.txt')
  console.log(r2.toString())
}

run(getFiles)
```
#### generator 配合 promisify 自动执行
```js
const promisify = function(fn, ...args) {
  return new Promise((resolve, reject) => {
    fn(...args, (...args) => {
      resolve(args)
    })
  })
}

const readFile = function(filePath, callback) {
  console.log(`读取：${filePath}`)
  setTimeout(() => {
    callback(null, `返回结果：${filePath}`)
  }, 2000)
}

const read = promisify.bind(null, readFile)

function* getFiles() {
  const r1 = yield read('/etc/a.txt')
  console.log(r1.toString())
  const r2 = yield read('/etc/a.txt')
  console.log(r2.toString())
}

function run(gen) {
  const it = gen()
  const next = ([err, data]) => {
    if (err) throw err
    const { value, done } = it.next(data)
    if (!done) {
      value.then(next)
    }
  }
  next([])
}

run(getFiles)
```
#### async 函数返回的 Promise 对象，必须等到内部所有 await 命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到 return 语句或者抛出错误。也就是说，只有 async 函数内部的异步操作执行完，才会执行 then 方法指定的回调函数。
```js
// 下面代码中，函数 getTitle 内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行 then 方法里面的 console.log
async function getTitle(url) {
  let response = await fetch(url);
  let html = await response.text();
  return html.match(/<title>([\s\S]+)<\/title>/i)[1];
}
getTitle('https://tc39.github.io/ecma262/').then(console.log)
```
#### async await 实现 javascript 休眠
```js
function sleep(interval) {
  return new Promise((resolve) => {
    setTimeout(resolve, interval)
  })
}
async function one2FiveInAsync() {
  for (let i = 1; i <= 5; i++) {
    console.log(i)
    await sleep(1000)
  }
}
one2FiveInAsync()
```
#### throw 方法被捕获以后，会附带执行下一条 yield 表达式。也就是说，会附带执行一次 next 方法。
```js
function* gen() {
  try {
    yield console.log('a')
  } catch (e) {
    // do nothing
  }
  yield console.log('b')
  yield console.log('c')
}
const it = gen()
it.next()
it.throw()
it.next()
```
#### generator 自动执行器
```js
function spawn(gen) {
  return new Promise((resolve, reject) => {
    const it = gen()
    function next(v) {
      const { value, done } = it.next(v)
      if (done) {
        return resolve(value)
      } else {
        Promise.resolve(value)
          .then(next)
          .catch((e) => {
            const { value: _value } = it.throw(e)
            Promise.resolve(_value).then(next)
          })
      }
    }
    next()
  })
}

function chainAmimationsGenerator(fns) {
  return spawn(function*() {
    let ret = null
    for (const fn of fns) {
      try {
        ret = yield fn()
      } catch (e) {
        console.log(e)
      }
    }
    return ret
  })
}
```
> 补充：Promise.try() 如何不管 f 是否包含异步操作，都用 then 方法指定下一步流程，用 catch 方法处理 f 抛出的错误。
#### 模块异步加载
```js
// awaiting.js
function fetchRes() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(123)
    }, 1000)
  })
}
export default (async function() {
  const dynamic = await fetchRes()
  console.log(dynamic)
  return dynamic
})()

// index.js
import promise from './awaiting'
promise.then((res) => {
  console.log(res)
})

```
> 补充：import() 动态加载模块
```js
// awaiting.js
export const x = 123
export default {
  a: 'xxx',
  b: 'yyy'
}

// index.js
function importModule() {
  const str = 'awaiting'
  import('./' + str).then(({ default: _default, x }) => {
    console.log(_default, x)
  })
}
setTimeout(() => {
  importModule()
}, 1000)
```