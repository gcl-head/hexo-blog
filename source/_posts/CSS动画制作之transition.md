---
title: CSS动画制作之transition
date: 2019-10-24 17:27:06
updated: 2019-10-24 17:27:06
tags:
    - css
    - transition
categories: frontend
---
transition是css的动画属性之一，用于设置元素的样式过渡。

## 1、使用方法

具体来说

1. 首先在元素的CSS中增加transition属性，在其中可以设置过渡动画的时长，可以为所有属性指定一个值，或者为不同属性指定不同时长

   ```css
   .box {
       border-style: solid;
       border-width: 1px;
       display: block;
       width: 100px;
       height: 100px;
       background-color: #0000FF;
       -webkit-transition:width 2s, height 2s, 
           background-color 2s, -webkit-transform 2s;
       transition:width 2s, height 2s, background-color 2s, transform 2s;
     	/*transition:2s*/
   }
   ```

2. 设置另一个CSS用于指定动画完成的状态，这样transition就会根据变化的CSS属性和时长执行动画

   ```css
   .box:hover {
       background-color: #FFCCCC;
       width:200px;
       height:200px;
       -webkit-transform:rotate(180deg);
       transform:rotate(180deg);
   }
   ```

上述即box被鼠标覆盖后会触发:hover的伪类，从而生成长达2s的动画

## 2、使用实例

我想要的不止如此，我希望可以在js中更加细致地控制动画的行程。

比如，点击图片触发js中的函数，触发图片翻转90度的动画，然后更改图片的src，再旋转图片90度，达到翻转后是一张全新图片的效果。

首先设置翻转前和翻转90度的CSS样式：

```css
.susi{
  /*翻转前*/
  width: 200px;
  height: 200px;
  border-radius: 50%;
  z-index: 1;
  transition: 0.5s;
}
.susi:hover{
  transform: scale(1.5);
}
.no_susi{
  /*翻转90度*/
    width: 200px;
    height: 200px;
    border-radius: 50%;
    z-index: 1;
    transform: rotateY(90deg);
    transition: 0.5s;
  }
```

然后设置点击图片后触发的函数：

```js
hideImg () {
  this.img_class = 'no_susi'
  setTimeout(this.changeImg, 500)
},
changeImg () {
  this.img_src = this.img_src === susi ? alligator : susi
  this.img_class = 'susi'
}
```

上述代码，具体来说就是手动改变图片使用的CSS类，从而触发transition动画。

然后等待0.5s动画执行结束，再改变动画src，恢复图片的CSS类。

---

由于setTimeout()函数并非真正的精准计时，因此使用setTimeout()函数执行多段的动画难免会造成动画卡顿等问题，因此也可以使用@transitionend属性直接在动画结束的时候调用changeImg()。

但由于该元素绑定了多个动画，每个动画结束都会调用@transitionend的方法，导致无法达到想要的效果。且假如子元素有transition属性，甚至也会冒泡调用父元素的@transitionend。因此需要额外的操作来处理这一问题，比如需要单独定义一个变量，在调用@transitionend方法的时候通过该变量来判断要进行什么操作。

另外上述使用实例点击首页三文鱼图像即可触发。

