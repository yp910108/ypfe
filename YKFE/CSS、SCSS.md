## 语法
- 缩进使用2个空格；
- 每个属性声明末尾都要加分号；
- 属性名全部使用小写字母，以中划线分隔。

## 空格
以下几种情况需要空格：
- 属性值前
- 选择器`>`、`+`、`~`前后
- `{` 前
- `!important`前
- `@else`前后
- 属性值中的`,`后
- 注释`/*`后和`*/`前

```html
/* bad */
.element {
  color :red!important;
  background-color: rgba(0,0,0,.5);
}

/* good */
.element {
  color: red !important;
  background-color: rgba(0, 0, 0, .5);
}

/* bad */
.element ,
.dialog{
  ...
}

/* good */
.element,
.dialog {
  ...
}

/* bad */
.element>.dialog{
  ...
}

/* good */
.element > .dialog {
  ...
}

/* bad */
.element{
  ...
}

/* good */
.element {
  ...
}
```

## 换行
以下几种情况不需要换行：
- `{`前

以下几种情况需要换行：
- `{`后和`}`前
- 每个属性独占一行
- 多个规则的分隔符`,`后

```html
/* bad */
.element
{color: red; background-color: black;}

/* good */
.element {
  color: red;
  background-color: black;
}

/* bad */
.element, .dialog {
  ...
}

/* good */
.element,
.dialog {
  ...
}
```

## 引号
- 最外层统一使用双引号；
- url的内容要用单引号；
- 属性选择器中的属性值需要引号。

```html
.element:after {
  content: "";
  background-image: url('logo.png');
}


li[data-type="single"] {
  ...
}
```

## 引号
- 最外层统一使用双引号；
- url的内容要用单引号；
- 属性选择器中的属性值需要引号。

```html
.element:after {
  content: "";
  background-image: url('logo.png');
}


li[data-type="single"] {
  ...
}
```

## 颜色
- 颜色16进制用小写字母；
- 颜色16进制尽量用简写。

```html
/* bad */
.element {
  color: #ABCDEF;
  background-color: #001122;
}

/* good */
.element {
  color: #abcdef;
  background-color: #012;
}
```

## 属性简写
- 属性简写需要你非常清楚属性值的正确顺序，而且在大多数情况下并不需要设置属性简写中包含的所有值，所以建议尽量分开声明会更加清晰；
- margin 和 padding 相反，需要使用简写；
- 常见的属性简写包括：font、background、transition、animation。

```html
/* not good */
.element {
  transition: opacity 1s linear 2s;
}

/* good */
.element {
  transition-delay: 2s;
  transition-timing-function: linear;
  transition-duration: 1s;
  transition-property: opacity;
}
```

## 其它
- 去掉小数点前面的 0；
- 属性值 '0' 后面不要加单位；
- 同个属性不同前缀的写法需要在垂直方向保持对齐，具体参照下面的写法；
- 无前缀的标准属性应该写在有前缀的属性后面；
- 为了使浏览器更快的渲染，建议书写顺序如下图所示。

```html
/* bad */
.element {
  color: rgba(0, 0, 0, 0.5);
}

/* good */
.element {
  color: rgba(0, 0, 0, .5);
}


/* bad */
.element {
  width: 0px;
}

/* good */
.element {
  width: 0;
}


/* not good */
.element {
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  border-radius: 3px;
}


/* good */
.element {
  -webkit-border-radius: 3px;
     -moz-border-radius: 3px;
          border-radius: 3px;
}


/* 书写顺序遵循 先写定位，然后按照盒子模型从外到内，最后写和空间大小无关的属性*/
/* bad */
.element {
  background-color: #ccc;
  color: #333;
  font-size: 14px;
  width: 100px;
  height: 100px;
  margin: 10px;
  padding: 20px;
  border: 1px solid #666;
  text-align: center;
  line-height: 100px;
  font-family: 'Microsoft YaHei';
  font-weight: 500;
  position: absolute;
  left: 0;
  top: 0;
}


/* good */
.element {
  position: absolute;
  left: 0;
  top: 0;
  margin: 10px;
  border: 1px solid #666;  
  padding: 10px;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
  font-family: 'Microsoft YaHei';
  font-weight: 500;
  font-size: 14px;
  background-color: #ccc;
  color: #333;
}
```

## 注释
- 注释统一用'/* */'，注释与文字间有空格，具体参照下面的写法；
- 缩进与下一行代码保持一致；
- 可位于一个代码行的末尾，与代码间隔一个空格。

```html
/* Modal header */
.modal-header {
  ...
}

/*
 * Modal header
 */
.modal-header {
  ...
}

.modal-header {
  /* 50px */
  width: 50px;
  color: red; /* color red */
}
```