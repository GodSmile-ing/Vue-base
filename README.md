# 学习资源

1、[vue基础知识](https://github.com/keepfool/vue-tutorials)<br>

2、[非常好的一些博文](http://www.cnblogs.com/xiaohuochai/tag/Vue/)<br>


# 1、vue-cart => 实现购物车和地址选配功能

### 前言

该案例来源于慕课网的教程[使用vue2.0实现购物车和地址选配功能](https://www.imooc.com/learn/796)。根据这门课程，做了一些总结和分析，进一步提升自己的vue使用水平。<br>

在文件夹目录中，[cart.html](https://github.com/CruxF/Vue-base/blob/master/vue-cart/cart.html)和[address](https://github.com/CruxF/Vue-base/blob/master/vue-cart/address.html)为原始代码，没有做过任何的修改，可以在充分理解该门课程下，自己动手实现一遍案例中的功能。因为vue-resource已经不被推荐使用，于是我用了axios替代了vue-resource来获取json中的数据。<br>

**【注意】：** <br>
由于本地的vue和vue-resource会面临过时的危机，而自己一开始也是因为使用别人那个过时vue-resource，一直无法取得json中的数据，所以我们最好通过CDN引入外部资源，该案例需要用到的相关资源地址：<br>
[vue官网](https://cn.vuejs.org/v2/guide/)<br>
[vue-resource](https://github.com/pagekit/vue-resource)<br>
[axios](https://github.com/axios/axios)<br><br>

### 训练方法

一步一个脚印，我们首先分析下**购物车** 实现所有功能的过程。<br>

1、渲染商品信息、商品金额、商品数量以及每一种商品的总额。<br>
【分析】<br>
由于以上所有的数据都保存在一个json文件中，那么我们肯定要先将所需要的数据取出来，然后使用一个数组将得到的数据保存起来。为了程序的健壮性和易读性，我们需要将获取json数据封装成一个方法，接着在某一个钩子函数中调用该方法。<br>

以上是对实例化一个Vue里面JavaScript代码的编写思路，下面我们就得寻思如何将获取到的数据渲染到html页面，这时候应该每个人都能想到使用v-for指令来循环，但是也要更深的思考一下：v-for的位置应该放在哪？v-for里面的值分别代表了哪些数据？图片的路径该如何绑定？总价格该怎么去计算？v-for里面是否还可以继续嵌套v-for？为什么需要在v-for里面再嵌套一层v-for？把这些慢慢地捋清楚了，对我们的编程思维会有很大的提升，从此也就能够举一反三了。<br><br>


2、格式化金额<br>
【分析】<br>
一般情况下，在一个Vue项目中要对某一类数据进行格式化，那么肯定得用到过滤器。既然如此，那我们的思路就很清晰了：首先得定义一个过滤器，在这个过程中得分清楚局部过滤器和全局过滤器的定义以及应用的场景，接着我们就得在html页面上调用这个过滤器，如何去调用？如何传递额外的参数？这些我们都得熟悉。<br><br>


3、单件商品金额计算<br>
【分析】<br>
商品金额与商品数量挂钩，想到商品数量的增减，一般人会定义两个不同的方法。其实完全没必要，我们只要在传递的参数上动动手脚就行了。这时候我们就要很清楚：单击数据改变，那么双向数据绑定是一定需要的，也需要在两个按钮中绑定事件，触发事件则调用方法。那么该如何传递参数？每一个参数又代表什么呢？商品数量的上下限怎么去设置？这些我们想明白后，下手就容易多了。<br><br>


4、商品单选功能。<br>
【分析】<br>
经过测试，我们在相应的元素添加上check类名，那单选按钮的样式就显示出来了。那这样有眉目了，下面先看一段代码解读。<br>
v-bind:class="{'check': item.checked}"含义是当item.checked为真的时候，check这个类名会被附加，即页面最终渲染会是class="item-check-btn check"。<br>
这时候我们就需要定义一个事件来改变item.checked的值，由于json文件中并没有checked这个属性，因此我们必须自己来注册属性，如何去注册一个对象的属性？这才是我们应该掌握的知识点。<br><br>


5、全选和取消全选
这段代码有点绕，讲不太清楚，我还是直接放代码，根据代码来理解。<br>
```
<div class="item-all-check">
  <a href="javascript:void 0">
    <span class="item-check-btn" :class="{'check':checkAllFlag}">
      <svg class="icon icon-ok"><use xlink:href="#icon-ok"></use></svg>
    </span>
    <span v-show="!checkAllFlag" @click="checkAll(true)">全选</span>
  </a>
</div>
<div class="item-all-del">
  <a href="javascript:void 0" class="item-del-btn" @click="checkAll(false)">
    <span v-show="checkAllFlag">取消全选</span>
  </a>
</div>
```
下面是JavaScript处理方法：<br>
```
checkAll: function (flag) {
  this.checkAllFlag = flag;
  var _this = this;
  this.productList.forEach(function (item, index) {
    if (typeof item.checked == 'undefined') {
      _this.$set(item, "checked", flag);
    } else {
      item.checked = flag;
    }
  });
}
```
根据上面两段代码来分析：点击全选，checkAllFlag的值变成true，而productList对象组里的对象checked属性要么被创建赋值为true，要么直接被赋值为true（已经被单选上的商品）。此刻对应的页面就是全选按钮样式被添加上，“全选”两个字消失，而“取消全选”两个字出现。对于点击取消全选，执行流程也是一样的，即不说了，需要时刻记住的是：Vue项目中数据是能够共享的。<br><br>


6、商品总金额计算<br>
【分析】<br>
商品的总金额 = 各类商品数量X各类商品金额。实现思路很简单，只要定义一个方法，当商品的按钮被点击选中的时候，那么计算总金额，需要注意的是金额的叠加问题和清除问题，然后就是在何时何地调用该方法，最后就是将总金额渲染到html页面。<br><br>


7、删除商品<br>
【分析】<br>
在html代码中，有一个蒙层效果`<div class="md-overlay" v-if="delFlag"></div>`，我们在删除商品这个过程中需要定义两个比较重要的点击事件：一个事件点击删除，我们需要把要删除的对象保存起来，同时显示出蒙层；另外一个事件就是要从对象组里面获取该对象的索引值，然后根据splice方法删除索引值从而达到删除对象的效果。<br><br>

关于购物车功能的实现思路就分析到这里，还有更多的小细节可以自己试着补充一下。如果觉得这分析欠妥的话，希望给些建议，疑义相与析，[点击查看演示](https://cruxf.github.io/Vue-base/vue-cart/cart01.html)。下面开始关于地址选项的分析。<br><br>


**地址选项分析：** <br>

1、渲染出所有地址<br>
【分析】<br>
这个和前面的购物车渲染商品信息大同小异，需要注意的是json里面的数据，因为有时候数据的不用，我们调取json数据要写的代码也是不一样的。<br><br>

2、开始默认显示3组数据<br>
【分析】<br>
要实现这个功能，我们需要明白的就是v-for指令里面不仅仅可以循环对象组，还可以循环方法，只要方法中返回的是一组对象即可，并且该方法要在计算属性中定义，因为在截取数据前，首先的缓存数据，截取数据使用的方法为：slice()。<br><br>

3、显示更多<br>
【分析】<br>
可以定义一个方法，改变数据截取的数量。下面介绍一个利用三元运算符来实现这个功能：<br>
`<a class="addr-more-btn up-down-btn" href="javascript:" @click="(limitNum == 3) ? limitNum=addressList.length : limitNum=3">`<br><br>

4、选中样式切换<br>
【分析】<br>
我们都知道，在v-for指令中，能将数据的索引值遍历出来，那么这个索引值有什么用呢？下面请看一段代码：<br>
`<li v-for="(item,index) in filterAddress" v-bind:class="{'check': index == currentIndex}" @click="currentIndex=index">`<br>
该段代码十分巧妙的使用索引值来实现点击切换样式，它的实现原理就像是：2 = y？然后点击事件触发的时候，就把y赋值为2（2在这象征的是索引值），因此2 = y为true，故check这个类被添加进去。<br><br>

5、设置默认地址<br>
【分析】<br>
在json文件中，我们有个属性是用来说明页面是显式“设为默认”还是“默认地址”，这个功能我们可以通过v-if去实现。当我们点击“设为默认”的时候，可以调用一个方法，传递的参数为当前对象的ID值，接着我们去遍历对象组，判断对象组里面是否有对象的ID值等于传递进来的对象ID值，如果相等，则把“默认地址”显示出来，而其他的则隐藏。<br><br>

6、配送方式选项<br>
【分析】<br>
实现思路与技巧都与第4步“选中样式切换”一样。可以看以下代码：<br>
```
<li v-bind:class="{'check': shippingMethod == 1}" @click="shippingMethod = 1">
  <div class="name">标准配送</div>
  <div class="price">Free</div>
</li>
<li v-bind:class="{'check': shippingMethod == 2}" @click="shippingMethod = 2">
  <div class="name">高级配送</div>
  <div class="price">180</div>
</li>
```
费了好长段时间，终于分析完了，[点击查看演示](https://cruxf.github.io/Vue-base/vue-cart/address01.html)。之前断断续续看了几遍视频，却总是不明就里，原来是过于浮躁，急于求成，导致浪费很多时间。其实对于技术，我现在才明白：不努力去理解代码，那么敲再多、敲再久能力也不会有任何的提高。<br><br>


# 2、vue-tab => Vue实现选项卡效果

这是一个利用Vue数据驱动特点做的一个选项卡，相比于纯JavaScript或者jQuery来说，方便简洁了不少。该案例原地址[请戳这里](https://juejin.im/post/5a0c191f6fb9a04514639419)<br>

案例的实现思路非常简单，一步步来说。<br>
1、点击顶部样式发生改变：<br>
当点击a标签的时候，会得到一个curId的属性和值，而类名为cur的class则根据curId属性的值来决定是否添加。<br>

2、底部文字变化：<br>
因为vue是根据数据的变化而实现某些功能的，数据驱动是vue的一大亮点。在点击a标签的时候，就创建了数据curId，v-bind:class和v-show都根据数据的真假而做出相应的行为。<br>

[点我查看效果呀](https://cruxf.github.io/Vue-base/vue-tab/index.html)<br><br>


# 3、vue-compre => Vue实现对数据的增删查

### 案例需求：
创建一个品牌展示表格，表头有编号（id），品牌名称（name），创建时间（time）和操作，需要实现的功能是对数据的增删查操作，和时间的格式化。这是一个[初始的静态页](https://cruxf.github.io/Vue-base/vue-compre/index01.html)<br>

**开发步骤一：** <br>
使用v-for指令遍历展示数据，最终呈现效果[点我看效果呀](https://cruxf.github.io/Vue-base/vue-compre/index02.html)<br>

**开发步骤二：** <br>
删除数据。首先，我们需要定义一个鼠标点击事件，并传入一个参数，当做被选中目标的ID值；接着我们要在Vue实例中定义一个方法，接收传递过来的参数，并将其保存下来，让对象的id等于传入的id；最后根据保存下来的id来删除指定的对象，在这过程中，要知道两个方法findIndex()和splice()[点我看效果呀](https://cruxf.github.io/Vue-base/vue-compre/index03.html)<br>

**开发步骤三：** <br>
增加数据。不用多想，使用双向数据绑定指令v-model，在静态页面中绑定相关的数据；接着将数据传递进一个方法中，把数据包装成数组要求的对象；最后将对象追加到数组中，并将输入框清空[点我看效果呀](https://cruxf.github.io/Vue-base/vue-compre/index04.html)<br>

**开发步骤四：** <br>
格式化时间。这里采用的全局过滤器，期间需要明白如何定义一个全局过滤器？如何调用全局过滤器？以及过滤器中各个参数的含义分别是什么[点我看效果呀](https://cruxf.github.io/Vue-base/vue-compre/index05.html)<br>

**开发步骤五：** <br>
查找数据。在这一过程中，v-for指令不再是遍历遍历已经被写好的数组对象里的数据了，而是要遍历一个在计算属性里定义的函数。在该函数里面，首先判断搜索框里面是否有值，如果没有，则返回原先的数组对象；如果有，那么就在原先数组上定义一个过滤器，在过滤器中又返回一个数组，然后在数组中查找存在的值并将其返回。在这过程中，要知道Object.keys()方法、some()方法、toLowerCase()方法以及indexOf()方法[点我看效果呀](https://cruxf.github.io/Vue-base/vue-compre/index06.html)<br>

**开发中所用到的方法：** <br>

1、findIndex()：返回传入一个测试条件（函数）符合条件的数组第一个元素位置[更多详情](http://www.runoob.com/jsref/jsref-findindex.html)<br>

2、splice()：向/从数组中添加/删除项目，然后返回被删除的项目[更多详情](http://www.w3school.com.cn/jsref/jsref_splice.asp)<br>

3、Object.keys()：会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 for...in 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）[更多详情](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)<br>

4、some()：只要数组中有一项在callback上被返回true，就返回true[更多详情](https://www.cnblogs.com/linsx/p/6796888.html)<br>

5、toLowerCase()：用于把字符串转换为小写。<br>

6、indexOf()：用来判断数组是否包含某个元素项目[更多详情](https://www.cnblogs.com/laneyfu/p/6136777.html)<br>

7、filter()：过滤筛选（callback在这里担任的是过滤器的角色，当元素符合条件，过滤器就返回true，而filter则会返回所有符合过滤条件的元素）[更多详情](https://www.cnblogs.com/linsx/p/6796888.html)<br>


# 4、vue-moudle => Vue各类组件传值的实现方式

1、父组件向子组件传值
首先在父组件定义好数据，接着将子组件导入到父组件中。父组件只要在调用子组件的地方使用v-bind指令定义一个属性，并传值在该属性中即可，此时父组件的使命完成，请看下面关键代码：
```
<div class="parentOne">
  <children-item :content="item" v-for="item in list" :key="item.id"></children-item>
</div>
```
【解析】
上面代码中`<children-item></children-item>`是导入进来的子组件，`content`是被v-bind指令定义的属性，当然不用v-bind指令定义也一样可以。只不过加了V-bind指令后，属性值就会被当做JavaScript表达式来解析，而不加v-bind指令就会被当做字符串解析。比如`Boolean="false"和:Boolean="false"`解析出来的结果是不同滴。如果还是有点迷糊，请来看[完整源码]()

好了，下面我们来看看子组件此时要做的事情是什么？

首先在子组件中要使用关键词`props`接收父组件传递过来的属性，然后直接对这个属性动手动脚就行了，十分简单，在这直接上完整源码：
```
<template>
  <div class="ChildrenOne">
    <ul>
      <li v-for="childItem in content">{{childItem}}</li>
      <li>{{message}}</li>
    </ul>
  </div>
</template>
<script>
  export default {
    name: 'ChildrenOne',
    props: ["content"],
    data() {
      return {
        message: this.content.id
      }
    }
  }
</script>
<style>

</style>
```













