---
date: 2019-11-19
tag:
  - window document
  - javascript
  - JS/jQuery宽高的理解和应用
author: Kimlon.zeng
location: Shenzhen
---

# JS/jQuery宽高的理解和应用

## window方向

outerHeight 浏览器外部高度

outerWidth 浏览器外部宽度

innerHeight 浏览器内部高度

innerWidth 浏览器内部宽度

outer、inner相关属性不兼容ie9以下的浏览器

### screen属性
screenTop浏览器距离顶部的高度

screenLeft浏览器距离左边的宽度

screen的width、height、availHeight、availWidth是固定不变的 跟屏幕相关。


## doucument方向
### client 该属性指的是元素可视区域宽高、即padding+content。如果没有滚动条，即为元素设置的高度和宽度，如果出现滚动条，滚动条会覆盖元素的宽度和高度，那么该属性就是其本来宽高减去滚动条宽高。
```
document.body.clientWidth
document.body.clientHeight
clientWidth=style.width+style.padding*2-滚动轴

clientTop === border-top 的 border-width // true
clientLeft === border-left 的 border-width // true
```

## offset方向
```
offsetWidth、offsetHeight这一对属性指的元素的border + padding + content宽度和高度。
假如无padding 无滚动 无border
offsetWidth = clientWidth = width
假如有paddding 无滚动 有border
offsetWidth = style.width+padding2+border2
offsetWidth = clientWidth+border*2
假如有padding 有滚动且滚动条显示，有border
offsetWidth = style.width+padding2+border2
offsetWidth = clientWidth+滚动轴宽度+border*2

offsetParent
如果当前元素的父元素没有进行css定位（position为absolute和relative）,offsetParent为body。
如果当前元素的父元素有进行css定位（position为absolute和relative）,offsetParent取最近的那个父元素。

**在ie6/7中
offsetLeft = offsetParent的padding-left + 当前元素的padding-left

在ie8/9/10 chorm
offsetLeft =offsetParent的margin-left +offsetParent的border宽度+ offsetParent的padding-left + 当前元素的padding-left

在ff中
offsetLeft = offsetParent的margin-left + offsetParent的padding-left + 当前元素的padding-left**```


## scroll方向

### document.body
```doucument.body的scrollWidth和scrollHeight与div中的scrollWidth和scrollHeight是有区别的

1给定宽高小于浏览器窗口时
scrollWidth通常等于浏览器宽度
scrollHeight通常等于浏览器高度

2给定宽高大于浏览器宽窗口，且内容小于给定宽高
scrollWidth通常等于其所有padding+margin+border+width
scrollHeight通常等于其所有padding+margin+border+height**

3给定宽高大于浏览器宽窗口，且内容大于给定宽高
scrollWidth通常等于其内容宽度+其padding+margin+border
scrollHeight通常等于其内容高度+其padding+margin+border

在某div中scrollWidth和scrollHeight
无滚动轴时
scrollWidth = clientWidth = width + padding2
有滚动轴时
scrollWidth = 实际内容宽度 + padding2
scrollHeight = 实际内容高度 + padding*2

scrollLeft scrollTop 属性可以读写 指的是当前元素中内容超出其宽高的时候，元素被卷起的高度和宽度。```
