# css-style

## 样式文件结构示例
```
style
├── components
├── core／normalize／reset
├── variables
├── mixins
├── utillties
├── global
└── index.less
```

## Normalize/Reset 保证跨浏览器一致性
[Normalize](http://nicolasgallagher.com/about-normalize-css/)

## Animation 动画
[animate.css](https://github.com/daneden/animate.css)

## Icon 字体图标
[fontawesome.io](http://fontawesome.io/)

## Global 项目的全局样式
[oocss.org](http://oocss.org/)

## Less/Sass 统一预处理
[Less](http://less.bootcss.com/)
[Sass](https://www.sass.hk/)

## Media

```
/* media */
/* 横屏 */
@media screen and (orientation:landscape){}
/* 竖屏 */
@media screen and (orientation:portrait){}
/* 窗口宽度<960,设计宽度=768 */
@media screen and (max-width:959px){}
/* 窗口宽度<768,设计宽度=640 */
@media screen and (max-width:767px){}
/* 窗口宽度<640,设计宽度=480 */
@media screen and (max-width:639px){}
/* 窗口宽度<480,设计宽度=320 */
@media screen and (max-width:479px){}
...
```
## Variables 全局css变量

```
# 统一颜色、字体(类别、大小、颜色)、行高、间距等
@primary:         #1ab394;
@success:         #1c84c6;
@info:            #23c6c8;
@warning:         #f8ac59;
@danger:          #ed5565;
...
```
## Mixins 常用的css功能


```
Mixins
.text-overflow() {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
...
```
## Utillties 通用的样式类名

```
.pull-right {
  float: right !important;
}
.pull-left {
  float: left !important;
}
...
```
## Name 命名规范
[BEM](http://getbem.com/naming/)

```
HTML

<form class="form form--theme-xmas form--simple">
  <input class="form__input" type="text" />
  <input
    class="form__submit form__submit--disabled"
    type="submit" />
</form>

CSS

.form { }
.form--theme-xmas { }
.form--simple { }
.form__input { }
.form__submit { }
.form__submit--disabled { }
```

## Css Scope 局部样式
[scope](https://vue-loader.vuejs.org/en/features/scoped-css.html)

```
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>
```
## Css Modules 模块化
[css module](https://github.com/css-modules/css-modules)

```
<style module>
.red {
  color: red;
}
</style>

<template>
  <p :class="$style.red">
    This should be red
  </p>
</template>
```

## Stylelint
[stylelint.io](https://stylelint.io/user-guide/rules/)

```
Color
  color-no-invalid-hex: Disallow invalid hex colors.
Font family
  font-family-no-duplicate-names: Disallow duplicate font family names.
...
```


