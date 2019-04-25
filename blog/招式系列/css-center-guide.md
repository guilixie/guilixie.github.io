# 常用的水平垂直居中解决方案

## 目录

## 水平居中

### inline 或 inline-* 元素

容器元素使用样式`text-align: center;`即可。

### block 元素

#### 1. 使用margin auto

定宽（非100%），项目元素使用样式`margin: 0 auto;`即可。

#### 2. 转换行内或行内块元素

不定宽（100%），容器元素使用样式`text-align: center;`，且项目元素使用样式`display: inline-block;` 或 `display: inline;`即可。

#### 3. 使用定位属性

#### 4. 使用flex或grid布局

## 垂直居中

## 水平垂直居中

## 总结

#### 参考文章

1. [Centering in CSS: A Complete Guide](https://css-tricks.com/centering-css-complete-guide/)

2. [CSS水平居中+垂直居中+水平/垂直居中的方法总结](https://blog.csdn.net/weixin_37580235/article/details/82317240)

3. [CSS实现水平垂直居中的1010种方式](https://yanhaijing.com/css/2018/01/17/horizontal-vertical-center/)