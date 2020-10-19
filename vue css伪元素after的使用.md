开篇一句话：CSS的伪类，伪元素就当成vue的过滤器使用就好了，我也是想到这个突然就茅塞顿开了。

用法：
:after 选择器表示向选定的元素之后插入内容。
要有content属性

需求举例：div按钮后面有个朝下的ico,点击div后 ico方向朝上
![点击前](https://upload-images.jianshu.io/upload_images/9645550-2fdc01cd9067e8dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![点击后](https://upload-images.jianshu.io/upload_images/9645550-61cb0f077be1dce6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实现办法:
1.可以使用JS实现，div的class名绑定到一个变量上，根据变量的真假值更换class 
```
 <div :class="[xxxvar?'class_1':'class_2']"></div>
```
2.伪类的实现方法
先说思路：
要借助两个class，基础class('base_class')和激活class('rotate').

base_class
相对定位。

base_class:after 
放ico朝上的样式，绝对定位。

rotate不要单独加after，不然会取代掉base_class的after,
要两个class都出现才加after
rotate绑定到一个布尔变量上，该变量的真假值由open_select_window更改
html:
```
<div :class="{'base_class':true,'rotate':open_small_window}" 
         @click.prevent="open_select_window">
</div>
```

```
        .base_class{
            position:relative;
            width: 1.6rem;
            text-align: right;
            color: #ff7500;
            font-size: .26rem;
            margin-right: 16px;
            height: 0.32rem;
            line-height: 0.32rem;
            padding-right: .28rem;
          }
          .base_class:after{
            content:'';
            position: absolute;
            display: block;
            top: .1rem;
            right: 0;
            width: .16rem;
            height: .16rem;
            background: url('../../../assets/images/icons/xiala@2x.png') no-repeat;
            background-size: .16rem, .1rem;
            background-position: right;
          }
          .base_class.rotate:after{
            transform:rotate(180deg)
          }
```