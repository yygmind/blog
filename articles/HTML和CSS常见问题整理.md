### HTML和CSS常见问题整理



* 盒子模型

![](http://www.runoob.com/images/box-model.gif)

对于现代浏览器来说，css中指定的width就是content width。

对于IE5.x和6来说，在怪异模式中width等于content、左右padding和左右border。



其中`padding`和`margin`的4种写法。

1、上  右  下  左

```css
padding:10px 5px 15px 20px;
```

2、上  右左  下

```css
padding:10px 5px 15px;
```

3、上下  右左

```css
padding:10px 5px;
```

4、四边一致

```css
padding:10px;
```



* `display`和`position`介绍

  * `display`规定元素应该生成的框的类型。

  | 值           | 描述                                                 |
  | :----------- | ---------------------------------------------------- |
  | flex         | 设置弹性容器                                         |
  | block        | 此元素将显示为块级元素，此元素前后会带有换行符。     |
  | inline       | 默认。此元素会被显示为内联元素，元素前后没有换行符。 |
  | inline-block | 行内块元素。（CSS2.1 新增的值）                      |
  | none         | 此元素不会被显示。                                   |
  | list-item    | 此元素会作为列表显示。                               |

  

  * `position`: 指定一个元素在文档中的定位方式，[`top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/top)，[`right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/right)，[`bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/bottom)和 [`left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/left) 属性则决定了该元素的最终位置。

  | 值       | 描述                                                         |
  | -------- | ------------------------------------------------------------ |
  | absolute | 生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。<br>元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。 |
  | fixed    | 生成绝对定位的元素，相对于浏览器窗口进行定位。<br>元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。 |
  | relative | 生成相对定位的元素，相对于其正常位置进行定位。<br>因此，"left:20" 会向元素的 left 位置添加 20 像素。 |
  | static   | 默认值。没有定位，元素出现在正常的流中<br>（忽略 top, bottom, left, right 或者 z-index 声明）。 |
  | inherit  | 规定应该从父元素继承 position 属性的值。                     |



* CSS选择器分类

  不同级别：排序：!important > 行内样式 > ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性。

  同一级别：后写的会覆盖先写的。



* 使元素消失的方法
  * `opacity：0`：该元素隐藏起来，但不会改变页面布局，如果该元素绑定了事件会触发。
  * `visibility:hidden`：该元素隐藏起来，但不会改变页面布局，不会触发该元素已经绑定的事件。
  * `display:node`：把元素隐藏起来，并且会改变页面布局，可以理解成在页面中把该元素删掉。



* 如何画一个三角形

  左右边框设置为透明，长度为底部边框的一半。左右边框长度必须设置，不设置则只有底部一条边框，是不能展示的。

  ```css
  .child{
    	width: 0;
      height: 0;
      border-bottom: 100px solid cyan;
      border-left: 50px solid transparent;
      border-right: 50px solid transparent;
  }
  ```

  

* 浮动相关

  * 为什么要清除浮动

    主要是为了解决父元素高度坍陷问题。

    一个块级元素如果没有设置`height`,其`height`由子元素撑开，对子元素使用了浮动之后，子元素就会脱离文档流。那么父元素中没有内容撑开其高度，这样父元素的`height`就会被忽略。

    

  * 如何清除

    ```css
    1、父元素设置overflow: hidden(少用)
    
    2、clearfix：使用内容生成的方式清除浮动，不会破坏文档流。
        .clearfix: after { 	 // :after选择器向选定的元素之后插入内容
            content:""; 	 // 生成内容为空
            display: block;  // 块级元素显示
            clear:both; 	 // 清除前面元素
        }
    ```



- 行内元素居中

  ```css
  .parent {
      text-align: center
  }
  ```



* DIV居中问题

  1、使用flex

  ```css
  .parent {
      height: 600px;
      border: 1px solid red;
      
      display: flex; 
      justify-content: center;
      align-items: center;
  }
  .child {
      border: 1px solid green;
    	width: 300px;
  }
  ```

  2、使用transform

  ```css
  .parent{
    height: 600px;
    border: 1px solid red;
    position: relative;
  }
  .child{
    border: 1px solid green;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
  }
  ```

  3、使用margin-top -一半的高度

  ```css
  .parent{
    height: 600px;
    border: 1px solid red;
    position: relative;
  }
  .child{
    border: 1px solid green;
    width: 300px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-left: -150px;
    margin-top: -50px;
  }
  ```

  4、使用绝对布局`absolute`和`margin:auto`

  ```css
  .parent{
    height: 600px;
    border: 1px solid red;
    position: relative;
  }
  .child{
    border: 1px solid green;
    position: absolute;
    width: 300px;
    height: 200px;
    margin: auto;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
  }
  ```



* 三栏布局

  要求两边两栏宽度固定，中间栏宽度自适应
  * 方案一：position绝对定位法

    **center的div需要放到后面**，将左右两边使用absolute定位，因为绝对定位使其脱离文档流，最后面的center会显示在正常文档流中，然后设置margin属性，留出左右两边的宽度。

    ```css
    .parent {
      border: 1px solid red;
      position: relative;
    }
    
    .child_left {
      width: 100px;
      height: 100px;
      border: 1px solid gray;
      position: absolute;
    }
    
    .child_right {
      width: 100px;
      height: 100px;
      border: 1px solid green;
      position: absolute;
      right: 0;
    }
    
    // div在html中必须放在left和right之后
    .child_center {
      width: 100px;
      height: 100px;
      border: 1px solid black;
      margin: auto
    }
    ```

    

  * 方案二：float 自身浮动法

    **center的div需要放到后面**，对左右使用float:left和float：right，float使左右两个元素脱离文档流，中间的正常文档流中，然后设置margin属性，留出左右两边的宽度。

    ```css
    .parent {
      border: 1px solid red;
      position: relative;
    }
    
    .child_left {
      width: 100px;
      height: 100px;
      border: 1px solid gray;
      float: left;
    }
    
    .child_right {
      width: 100px;
      height: 100px;
      border: 1px solid green;
      float: right;
    }
    
    .child_center {
      width: 100px;
      height: 100px;
      border: 1px solid black;
      margin: auto
    }
    ```



* 介绍BFC

  BFC是CSS布局的一个概念，是一块独立的渲染区域，是一个环境，里面的元素不会影响到外部的元素 。

  * 如何生成BFC：（即脱离文档流） 	     
    * 1、根元素，即HTML元素（最大的一个BFC） 	     
    	 2、float的值不为none 	     
    	 3、position的值为absolute或fixed 	     
    	 4、overflow的值不为visible（默认值。内容不会被修剪，会呈现在元素框之外） 	     
    * 5、display的值为inline-block、table-cell、table-caption 

  

  * BFC布局规则：

    * 1.内部的Box会在垂直方向，一个接一个地放置。 	     

    	 2.属于同一个BFC的两个相邻的Box的margin会发生重叠 	    

    	 3.BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此, 文字环绕效果，设置float 	     

    	 4.BFC的区域不会与float box重叠。 	     

    * 5.计算BFC的高度，浮动元素也参与计算

      

  * BFC作用：

    * 1.自适应两栏布局 	 
    	 2.可以阻止元素被浮动元素覆盖 	 
    	 3.可以包含浮动元素---清除内部浮动 原理：触发父div的BFC属性，使下面的子div都处在父div的同一个BFC区域之内 	 
    * 4.分属于不同的BFC时，可以阻止margin重叠

   

* CSS3特性 `vh`和`vw`

  * `vh` 相对于视窗的高度，视窗高度是100vh
  * `vw` 相对于视窗的宽度，视窗宽度是100vw

  这里是视窗指的是**浏览器内部的可视区域大小**，即`window.innerWidth/window.innerHeight`大小，不包含任务栏标题栏以及底部工具栏的浏览器区域大小。

  * 可以通过**@supports**来判断是否支持某个css属性。

  ```css
  @supports (property: value){
    element {
      property: value;
    }
  }
  
  // 例
  @supports (display: flex){
    .flex {
      display: flex;
      float: none;
    }
  }
  ```

  



