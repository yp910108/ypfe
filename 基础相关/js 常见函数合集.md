#### 深度合并
```js
function deepMerge(...objs) {
  const result = Object.create(null)
  objs.forEach(obj => {
    if (!isPlainObject(obj)) return
    Object.keys(obj).forEach(key => {
      const val = obj[key]
      if (isPlainObject(val)) {
        if (isPlainObject(result[key])) {
          result[key] = deepMerge(result[key], val)
        } else {
          result[key] = deepMerge(val)
        }
      } else {
        result[key] = val
      }
    })
  })
  return result
}
```
#### 根据子级查找父级
```js
function findParents(originList, originId) {
  const ret = []
  function temp(list, id) {
    for (let i = 0, len = list.length; i < len; i++) {
      const { id: currId, parentId, children } = list[i]
      if (currId === id) {
        if (parentId || parentId === 0) {
          ret.unshift(parentId)
        }
        temp(originList, parentId)
        break
      }
      if (children && children.length) {
        temp(children, id)
      }
    }
  }
  temp(originList, originId)
  return ret
}
```
#### 查找选中的节点（不包括半选）
```js
function getCheckeds (menus) {
  let result = []
  function temp (menus) {
    const ret = []
    menus.forEach((menu) => {
      const { id, checked, children } = menu
      if (children && children.length) {
        const checkeds = temp(children)
        if (checkeds.length === children.length) {
          ret.push(id)
        }
      } else {
        if (checked) {
          ret.push(id)
        }
      }
    })
    result = [...result, ...ret]
    return ret
  }
  temp(menus)
  return result
}
```
#### 函数节流
```js
function debounce(func, delay) {
  let timer
  return function(...args) {
    if (timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => {
      func.apply(this, args)
    }, delay)
  }
}
```
#### 通过字符串`a.b.c`查找`obj.a.b.c`
```js
function findObject(obj, exp = '') {
  const arr = exp.split('.') // 'a.b.c' => '[a, b, c]'
  arr.forEach((key) => {
    if (key) {
      obj = obj[key]
    }
  })
  return obj
}
```
#### 将十进制转为二进制
```js
function toBinary(num) {
  let ret = ''
  while (num) {
    ret += num % 2
    num = Math.floor(num / 2)
  }
  return ret
    .split('')
    .reverse()
    .join('')
}
console.log(toBinary(4294967295))
```
#### js 操作样式添加前缀
```js
const _vender = (() => {
  const vendors = ['t', 'OT', 'msT', 'MozT', 'webkitT']
  const domStyle = document.createElement('div').domStyle
  for (const [i, v] of vendors.entries()) {
    if (`${v}ransform` in domStyle) {
      return v.slice(0, v.length - 1)
    }
  }
})()
function prefixStyle (style) {
  if (!_vendor) return style
  return _vendor + style.charAt(0).toUpperCase() + style.slice(1)
}
```
#### js 获取参数（query）对象
```js
function getQueryObject(url) {
  url = url || window.location.href
  const search = url.slice(url.indexOf('?'))
  const obj = {}
  const reg = /([^?&=]+)=([^?&=]*)/g
  search.replace(reg, (_, $1, $2) => {
    const name = decodeURIComponent($1)
    let val = decodeURIComponent($2)
    val = String(val)
    obj[name] = val
    return _
  })
  return obj
}
```
#### 判断是否是绝对路径
```js
function isExternal(path) {
  return /^(https?:|mailto:|tel:)/.test(path)
}
```
#### 拼接url
```js
function combineURL(baseURL, relativeURL) {
  return relativeURL ? baseURL.replace(/\/+$/, '') + '/' + relativeURL.replace(/^\/+/, '') : baseURL
}
function combineURL(...urls) {
  const ret = urls.map((url) => {
    if (!url) return url
    if (isExternal(url)) {
      return url.replace(/\/+$/, '')
    } else {
      return url.replace(/^\/+/, '').replace(/\/+$/, '')
    }
  })
  const url = ret.filter((r) => r).join('/')
  return isExternal(url) ? url : `/${url}`
}
```
