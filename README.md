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

关于购物车功能的实现思路就分析到这里，还有更多的小细节可以自己试着补充一下。如果觉得这分析欠妥的话，希望给些建议，疑义相与析。下面开始关于地址选项的分析。<br><br>


**地址选项分析：** <br>

1、渲染出所有地址，这个和前面的购物车渲染商品信息大同小异







