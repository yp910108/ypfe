## 语法
- 缩进使用2个空格；
- 嵌套的节点应该缩进；
- 在属性上使用双引号，不能使用单引号；
- 属性名全小写，用中划线做分隔符；
- 标签名小写，自动闭合标签结尾使用斜线 '/'；
- 标签的自定义属性以 data- 开头，如：`<a href="#" data-num="18">1</a>`
- 除非有特定的功能、组件要求等，禁止随意使用 id 来定义元素样式；

## HTML5 doctype
在页面开头使用这个简单地 doctype 来启用标准模式，使其在每个浏览器中尽可能一致的展现；<br/>
虽然 doctype 不区分大小写，但是按照惯例，doctype 大写。
```html
<!DOCTYPE html>
<html>
  ...
</html>
```

## 字符编码
通过声明一个明确的字符编码，让浏览器轻松、快速的确定适合网页内容的渲染方式，通常指定为'UTF-8'。
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8"/>
  </head>
  ...
</html>
```

## IE兼容模式
用 <meta> 标签可以指定页面应该用什么版本的IE来渲染；
```html
<!-- PC端 META -->
<meta charset="UTF-8"/>
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">

<!-- 移动端 META -->
<meta charset="UTF-8"/>
<meta name="viewport" content=“width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no”>
```

## 引入 CSS，JS
根据HTML5规范, 通常在引入CSS和JS时不需要指明 type，因为 text/css 和 text/javascript 分别是他们的默认值。
```html
<!-- External CSS -->
<link rel="stylesheet" href="code-guide.css"/>

<!-- In-document CSS -->
<style>
  ...
</style>

<!-- External JS -->
<script src="code-guide.js"></script>

<!-- In-document JS -->
<script>
  ...
</script>
```

# 属性顺序
属性应该按照特定的顺序出现以保证易读性。
- id
- type
- data-*
- name
- class
- placeholder
- required、readonly、disabled
- href、src、value

## boolean属性
boolean属性指不需要声明取值的属性，boolean属性的存在表示取值为true，不存在则表示取值为false。
```html
<input type="text" disabled/>

<input type="checkbox" value="1" checked/>

<select>
  <option value="1" selected>1</option>
</select>
```

## 减少标签数量
在编写HTML代码时，需要尽量避免多余的父节点；很多时候，需要通过迭代和重构来使HTML变得更少。
```html
<!-- Not well -->
<span class="avatar">
  <img src="..."/>
</span>

<!-- Better -->
<img class="avatar" src="..."/>
```

## 注释
统一使用 `<!-- -->` 进行注释，注释与文字之间有空格