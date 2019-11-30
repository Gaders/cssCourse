## 再来看看浮动

> float CSS属性指定一个元素应沿其容器的左侧或右侧放置，允许文本和内联元素环绕它。该元素从网页的正常流动中移除，尽管仍然保持部分的流动性（与绝对定位相反）。

​	float的基础效果大家都是熟悉的不能再熟悉了，在这些特性中有一点要牢记就是元素从页面常规流中移除了，其他元素无法感知到它的存在，但是文本和内联元素可以环绕它。换句话说就是文字还能**感受到浮动元素的边界**,比如下面这个栗子

![](https://user-images.githubusercontent.com/15126694/30705654-9bf94150-9f28-11e7-8ae7-796bebaacedd.png)



​	这种还能让文字围绕浮动元素而显示的情况就是其仍保持着部分流动性的证明，如果是彻底的脱离了正常流，比如使用了display：absolute绝对定位，我们就会看到这种效果。这一回文字也不能感受到元素的边界了，绝对定位的元素已经彻彻底底得脱离了文档流。	

​														![](https://user-images.githubusercontent.com/15126694/30706062-d7dc17fa-9f29-11e7-8f86-02b61b16ec7c.png)

​	对比完float和绝对定位的区别之后，再让我们把视线移回float，如果这一次浮动元素的周围不再是文字，而是再普通不过的元素呢?

​	显然，这种情况下普通元素是感知不到float元素的存在的，图中绿色方块和红色方块已经重合了，而并不是像普通文档流中的元素一样相邻显示。

![](https://user-images.githubusercontent.com/15126694/30706243-5e6adb26-9f2a-11e7-9889-f0adcf1fbf2f.png)

由此：我们可以就能够引出关于float的相当重要的情形了

- 浮动引起的父元素高度坍塌

- 如何清除浮动		

### 父元素的高度坍塌

 	先举一个荔枝

```html
<style>
    #parent {
        background-color: green;
    }
    .child1 {
    	width: 100px;
        height: 100px;
        background-color: red;
        float: left;
    }
    .child2 {
        width: 100px;
        height: 100px;
        background-color: blue;
        float: left;
    }
    .child3 {
        width: 100px;
        height: 100px;
        background-color: yellow;
        float: left;
    }
</style>    

<div id="parent">                     <!-- 绿色背景 -->
        <div class="child1"></div>    <!-- 红色背景 -->
        <div class="child2"></div>    <!-- 蓝色背景 -->
        <div class="child3"></div>    <!-- 黄色背景 -->
</div>
```

​	对于现在的各位来说，这应该是一段再简单不过的html代码了。那么现在我们能在页面上看到几种颜色的方块呢？

​	其中的原因大家应该也都很清楚了吧，就是上面提到的浮动元素引起的父元素高度坍塌。父元素parent没有设置高度，其高度全靠三个在文档流的子元素来支撑，一旦子元素统统浮动起来脱离了文档流，父元素失去了顶梁柱。自然就拉跨了。

​	这个问题显然不能就放在那不管，虽然我们可能可以去人为设置其父元素的高度，但是这样设置的高度就是我们平时所说的“写死了”。我们会得到一个高度无法自适应内容的div，这对一些可能的情况下来说毫无疑问是不利的。比如下面这种情况

```html
<style>
        #parent {
            background-color: green;
            height:100px;
            margin-top: 100px;
            outline: 4px black solid;
        }
        .child1 {
            width: 100px;
            height: 96px;
            border: 2px white solid;
            background-color: red;
            float: left;
            color: white;
            font-size: 48px;
        }
        #parent::after {
            content: '';
            clear: both;
            display: block;
        }
</style>    
<button class="btn">点我开始南上加南</button>
<div id="parent">                     <!-- 绿色背景 -->
     <div class="child1">南</div>     <!-- 红色背景 -->
</div>
<script>
        const parent = document.querySelector("#parent")
        const btn = document.querySelector(".btn")
        btn.addEventListener("click", () =>{
            let child = document.createElement('div')
            child.className = 'child1'
            child.innerText = '南'
            parent.insertBefore(child, parent.children[0])
        });
</script>
```

​	试着把这个parent想象成这个区域的背景色，用户点击按钮能往这个区域中添加方块，这时候如果你的parent的高度像上面一样被写死了，那么当方块的数量大于一排的时候下面的方块就会离开背景的包裹，这显然---会被视觉叫去回炉重造。。。。

​	那么如果我们即想要父元素高度能够自适应，并且还想继续使用我们的浮动来布局，那么我们应该做什么呢?

​						![](https://imgsa.baidu.com/forum/w%3D580/sign=e038f3580a082838680ddc1c8898a964/b9ccd8cfc3fdfc035502bbffd93f8794a4c22634.jpg)

### 清除浮动

​	清除浮动说白了就是解决上面的父元素高度坍塌的问题，所以我们要想办法去恢复父元素的高度。

直接先上一个粒子。

```html
<style>
        #parent {
            background-color: green;
            height:100px;
            margin-top: 100px;
            outline: 4px black solid;
        }
        .child1 {
            width: 100px;
            height: 96px;
            border: 2px white solid;
            background-color: red;
            float: left;
            color: white;
            font-size: 48px;
        }
        #parent::after {
            content: '';
            clear: both;
            display: block;
        }
        .clear {
        	clear: both;
       	}
</style>    
<button class="btn">点我开始南上加南</button>
<div id="parent">                     <!-- 绿色背景 -->
     <div class="child1">南</div>     <!-- 红色背景 -->
     <div class="clear"></div> 
</div>
<script>
        const parent = document.querySelector("#parent")
        const btn = document.querySelector(".btn")
        btn.addEventListener("click", () =>{
            let child = document.createElement('div')
            child.className = 'child1'
            child.innerText = '南'
            parent.insertBefore(child, parent.children[0])
        });
</script>
```

​	你可能会发现，这段代码只是比上面的原版多了一个class名为clear的div，而我们对于它的样式只是写了一行` clear: both`我们甚至没给这个div宽高，但是自适应的效果却出来了。那么这一行代码究竟干了什么呢？

>clear CSS 属性指定一个元素是否可以在它之前的浮动元素旁边，或者必须向下移动(清除浮动) 在它的下面。clear 属性适用于浮动和非浮动元素。

​	也许我们可以换一种更加容易理解但是不大准确的说法，clear属性让这个div向下移动到 ：另起一行的程度，而这个div我们又没有为其设置高度，所以就是一个高度为0的另起一行的元素，因为高度为0所以我们看不到它，但是这个另起一行的效果成功让父元素体会到了来自内部的高度支撑。所以实现了清除浮动的效果。

​	由此可以引出几种思路类似的清除浮动的方式

- **使用带clear属性的空元素** 

  优点：简单，代码少，浏览器兼容性好。
  缺点：需要添加大量无语义的html元素，代码不够优雅，后期不容易维护。

- **使用CSS的overflow属性**
  这种方法下面讲，是属于通过触发BFC来实现的清除浮动

- **给浮动的元素的容器添加浮动**
  给浮动元素的容器也添加上浮动属性即可清除内部浮动，但是这样会使其整体浮动，影响布局，**不推荐使用。**

- **使用CSS的::after伪元素**

  这种方法相对于上面的几种方法是比较推荐使用的方法，如何使用下面讲

### BFC

>一个块格式化上下文（Block Formatting Context） 是Web页面的可视化CSS渲染出的一部分。它是块级盒布局出现的区域，也是浮动层元素进行交互的区域。

​	说的粗俗一点，BFC创建了一个自己的块，不干扰别人，别人不干扰它。

![](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3050950079,2515593550&fm=26&gp=0.jpg)

#### BFC的创建方法

- **根元素**或其它包含它的元素；
- **浮动** (元素的`float`不为`none`)；
- **绝对定位元素** (元素的`position`为`absolute`或`fixed`)；
- **行内块**`inline-blocks`(元素的 `display: inline-block`)；
- **表格单元格**(元素的`display: table-cell`，HTML表格单元格默认属性)；
- `overflow`的值不为`visible`的元素；
- **弹性盒 flex boxes** (元素的`display: flex`或`inline-flex`)；

#### BFC的作用及其特性

- **同一个 BFC 下外边距会发生折叠**

  ![](https://pic4.zhimg.com/v2-0a9ca8952c83141250a2d9002e6d2047_b.png)

  ```html
  <head>
  div{
      width: 100px;
      height: 100px;
      background: lightblue;
      margin: 100px;
  }
  </head>
  <body>
      <div></div>
      <div></div>
  </body>
  ```

  ​	观察这段html代码的结构，我们可以发现两个div都在一个根元素body标签的包裹下，这达成了触发BFC的第一种方法。因此两个div处于BFC当中，这时候就会发现他们的上下边距重叠了。我们对两个div都设置了100px的间距，然而实际效果只有100px而不是100+100=200。这不是 CSS 的 bug，我们可以理解为一种规范，**如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中。**

  ```html
  <div class="container">
      <p></p>
  </div>
  <div class="container">
      <p></p>
  </div>
  ```

  ```css
  .container {
      overflow: hidden;
  }
  p {
      width: 100px;
      height: 100px;
      background: lightblue;
      margin: 100px;
  }
  ```

  ​	此时我们可以发现这回两个div的间距是我们常规理解中的1 + 1 = 2了。

- **BFC 可以包含浮动的元素（清除浮动）**

  ```html
  <div style="border: 1px solid #000;overflow: hidden">  //设置overflow: hidden触发BFC
      <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
  </div>
  ```

- **BFC 可以阻止元素被浮动元素覆盖**

  ```html
  <div style="height: 100px;width: 100px;float: left;background: lightblue">
      我是一个左浮动的元素
  </div>
  <div style="width: 200px; height: 200px;background: #eee; overflow: hidden;">
      我是一个没有设置浮动, 也没有触发 BFC 元素, width: 200px; height:200px;
  </div>
  ```

  ​	这种就是浮动的一种经典情况，虽然文字能够环绕浮动元素不被覆盖，但是普通元素还是避免不了被覆盖，这时候通过对普通元素触发BFC的形式，让其不受外部元素影响就可解决被覆盖的问题。



## 伪元素

​	CSS的选择器除了根据id、class、属性等从DOM中获取元素的以外，还有很重要的一类，用来获取元素的特别内容或特别状态，这就是 **伪元素**(Pseudo-elements) 和 **伪类**(Pseudo-classes)。

​	其中伪元素是2个以冒号`::`作为前缀，被添加到一个选择器末尾。

### ::selection

​	::selection 选择器匹配被用户选取的选取是部分。

​	只能向 ::selection 选择器应用少量 CSS 属性：color、background、cursor 以及 outline。

​	比如我们可以让被选中的文本颜色变红。

```
<style>
p::selection {
	color: red;
}
</style>
<p>这是一段测试用的文本。</p>
```



### ::first-letter和::first-line

​	前者是选中某块级元素第一行的第一个字母，并且文字所处的行之前没有其他内容。

​	后者是在某块级元素的第一行应用样式。

​	有一说一，这两个其实用的很少，至少是我基本没怎么用过，了解一下有这些东西就行了

```
<style>
p::first-letter {
	color: red;
}
div::first-line {
	color: red;
}
</style>
<p>这是一段测试用的文本。</p>
<div>这是另外一段测试的文本</div>
```



### ::before 和 ::after

​	before和after伪类是用于在元素内容之前/后插入额外生成的内容。他们向文档树中加入内容，这些内容并不存在于HTML源代码中，但确是可见的，并且可以当作元素的子对象对待(正常的样式继承等)。![](https://s2.ax1x.com/2019/11/30/QE2WPe.png)

他们可以拿来做什么？

- 画分割线

  ```css
  .hr {
      text-align: center;
      margin-top: 100px;
  }
  .hr:after, .hr:before {
      content: '';
      position: absolute;
      top:110px;
      width: 200px;
      height: 1px;
      background-color: black;
  }
  .hr::after {
      right: 0;
  }
  .hr::before {
      left: 0;
  }
  
  <p class="hr">我是分割线</p>
  ```

  

- 清除浮动

  ```css
  .demo::after {
  	content: '';
  	clear:both;
  }
  ```

  

- 为你的按钮增大可相应的范围

  ```html
  <style>
  .btn {
      position: relative;
      left: 100px;
      top: 100px;
      border: 0 transparent;
      width: 100px;
      height: 50px;
      border-radius: 25%;
      background-color: aqua;
      transition: 1s;
  }
  .btn:hover {
      background-color: tomato;
  }
  .btn::before {
      content: '';
      position: absolute;
      top: -100px;
      left : -100px;
      right:-100px;
      bottom: -100px;
  }         
  </style>
  <button class="btn">
      鼠标移上来试试
  </button>
  ```

  ​	当然想实现增大按钮相应的范围不止这一种方法，但是这种既不需要js又不需要多写标签的方法还是挺精简的。

- 使用伪元素来实现单标签画图

  ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAIUAAAB5CAIAAACY6HUJAAABkElEQVR4nO3asW0DMRAAwe9M6kzqnA4c2ZmfR3sBzxZwIDDgMeG1VOr66wPoSzxa8WjFoxWPVjxa8WjFoxWPVjxa8Wj13ePSrXi0OusxNf0/xKMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFo9Xg8rut6vV4j03gM9Hw+r+t6v9/7o3jM9Emyf0t4jDVCwmOy/cXFY7jN553HfDuLi8eRbi8uHqe6d0t4HOwGCY+Dfb7tP9paPE51A2PxONQ9jMXjRLcxFo/xdjAWj9k2MRaPwfYxFo+pRjAWj5GmMBaP/QYxFo/9/GdoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1Y8WvFoxaMVj1a/4aGfxqPVKQ/9bTxa8WjFoxWPVh/V3k9r/TBOIgAAAABJRU5ErkJggg==)

  如图所示的一个类似聊天气泡的图形，想想要如何用css画出来

  首先我们要知道一个基本操作，如何用css画出三角形，这个想必大家应该都有所了解了，这里就只一个朝右的三角的代码示例	

  ```css
  .triangle{
    margin:30px;
    height:0px;
    width:0px;
    border-top:solid 100px transparent;
    border-bottom:solid 100px transparent;
    border-left:solid 100px red;
  }
  ```

  然后就可以利用三角形和伪元素来制作上面的图形了

  ```html
  <style>
  
  #demo{
    width:100px;
    height:100px;
    border:2px solid #000;
    position:relative;
  }
  
  /* 白色小三角形叠加在黑色大三角形上面，就只剩下2px的黑边了，实现了右边的小凸起效果 */
  
  #demo::before{
    content:"";
    width:0;
    height:0;
    position:absolute;
    left:100px;
    top:18px;
    border-top:solid 12px transparent;
    border-left:solid 12px black;       /* 黑色大三角形 */
    border-bottom:solid 12px transparent;
  }
  
  #demo::after{
    content:"";
    width:0;
    height:0;
    position:absolute;
    left:100px;
    top:20px;
    border-top:solid 10px transparent;
    border-left:solid 10px white;     /* 白色小三角形 */
    border-bottom:solid 10px transparent;
  }
  </style>
  
  <div id="demo"></div>
  ```

  


## 伪类

>CSS伪类是用来添加一些选择器的特殊效果。
>伪类是基于元素的特征而不是他们的id、class、属性或者内容。由于状态的变化是非静态的，所以元素达到一个特定状态时，它可能得到一个伪类的样式，所以它是基于文档之外的抽象。

​	伪类以冒号`:`作为前缀，被添加到一个选择器末尾的关键字，例如：`:active :hover :link :visited`。这里介绍几种常用的伪类的写法，其他的伪类可以在需要时自行查阅

- :hover		           鼠标滑过元素时会产生的效果，使用较多，相信大家也应该都有体会过，可与transition过渡属性搭配来实现一些效果
- :nth-child()           找到所有当前元素的兄弟元素中符合条件的元素，例如2n+1就是查找1，3，5，7等。常用于交替为一组元素设置不同的样式，例如灰白背景色交替的表格。文字颜色交替显示的ul li列表等。
- :first-child            一组兄弟元素中的第一个元素
- :last-child             一组兄弟元素中的最后一个元素
- :visited                 表示用户已访问过的链接

## 一些其他的css属性

  ### transition

transition 属性设置元素当过渡效果，四个简写属性为：

- transition-property                       设置你要产生过渡效果的属性
- transition-duration                       设置过渡动画的时间
- transition-timing-function           设置过渡动画随时间应该如何变化
- transition-delay                             设置过渡动画延迟多久开始波放

他 们的默认值分别为 all 0 ease 0，一般情况下我们可能会这么简写

```css
div
{
    width:100px;
    transition: width 2s;
}
div:hover {width:300px;}
```

​	这段代码反正不是我编的，是从菜鸟教程上cv下来的，实现的效果也很简单，就是当鼠标移入div时让宽度属性产生过渡效果，其过渡时间为2s。

​	关于transition-timing-function这个属性，其默认值ease就是一种先快后慢的类似阻尼的效果。其他配置方法的话如下图：

![](https://s2.ax1x.com/2019/11/30/QE2ba8.png)

​	一般常用的也就是ease和linear两种过渡的方式。

​	在你的网页中适当地加入一些过渡的效果会让整个网页的体验变好（讲人话：看起来牛逼），但是也别一个劲地加过渡效果。

​	一个简单的问题，如果我们想使用transition配合display来实现一个元素的淡入淡出会怎么样

```html
<style>
.demo {
    width: 100px;
    height: 100px;
    background-color: yellow;
    display: block;
}
.demo:hover {
    display: none;
}
</style>    
<div class="demo">
	企图淡入淡出
</div>
```



### filter

​	filter（滤镜）也是css里一个很强大的属性，滤镜为我们提供更多，更完备的修改图片可视效果的方法。（其实就是p图）。小到我们平时使用opacity(*%*)去设置一个图片的透明度，大到转换图片的色相饱和度，灰度，反转图像。filter都能提供给我们一种相对来说快捷的方法去实现它。

​	  (具体的示例直接戳)[https://www.runoob.com/cssref/css3-pr-filter.html]

## 渲染原理

### 浏览器渲染原理

1. 浏览器在接收到服务器返回的html页面后，

2. 浏览器开始**构建DOM树**`DOM TREE`，遇到CSS样式会构建**CSS规则树**`CSS RULE TREE`，

3. 遇到`javascript`会通过`DOM API`和`CSSDOM API`来操作`DOM Tree`和`CSS Rule Tree`，解析完成后，

4. 浏览器引擎会通过`DOM Tree` 和`CSS Rule Tree` 来构造 `Rendering Tree`（渲染树），

5. 最后，渲染树构建完成后就是 **"布局"** 处理，也就是确定每个节点在屏幕上的确切显示位置

6. 下个步骤（渲染之后），开始 **"绘制"** ，遍历渲染树，并用UI后端层，将每一个节点绘制出来。

   ![](https://upload-images.jianshu.io/upload_images/8646214-c6a28b94fa802b8e.png?imageMogr2/auto-orient/strip|imageView2/2/w/641/format/webp)

### css渲染规则

   ​	css的渲染规则，是从上到下，从右到左渲染的。例如下面这个样式

   ```css
   .main h4 a { font-size: 14px; }
   ```

​		渲染过程是这样的：首先先找到所有的 a，沿着 a 的父元素查找h4，然后再沿着 h4，查找.main。中途找到了符合匹配规则的节点就加入结果集。如果找到根元素的 html 都没有匹配，则这条路径不再遍历。下一个 a 开始重复这个查找匹配，直至没有a继续查找。

### 选择器权值

   ​	权值代表着优先级，权值越大，优先级越高。同种类型的选择器权值相同，后定义的选择器会覆盖先定义的选择器。

- important最高

- **内联：** 1000

- **ID：** 100

- **class/属性选择器/伪类：**10

- **Tag：** 1

  注：组合使用时权值会叠加

```html
<style>
    ul li {
        color: red;
    }
    ul .li4 {
        color: green;
    }
    #li3 {
        color: blue
    }
    ul .li3 {
        color: yellow
    }
    .li2 {
        color: grey
    }
</style>    
<ul>
    <li>1</li>
    <li class="li2" style="color: black">2</li>
    <li class="li3" id="li3">3</li>
    <li class="li4">4</li>
</ul>
```

