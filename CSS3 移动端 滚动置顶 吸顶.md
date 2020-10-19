如果是在回调里置顶DIV的话（比如点击事件），可以用scrollIntoView。
CSS3滚动置顶目前有两种解决方案：
第一种（主流）：transform: translate3d(x,y,z)
第二种（未来）：position: sticky;top:xxx;

假设需求：
![image.png](https://upload-images.jianshu.io/upload_images/9645550-5c5a7b5e6d09ed86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需求一：滚动过程中A ,C区域不吸顶，B区域吸顶

VUE框架推荐直接使用vantUI插件,indexbar索引栏。
[https://youzan.github.io/vant/#/zh-CN/index-bar](https://youzan.github.io/vant/#/zh-CN/index-bar)
vantUI原理用的是transform。
以上面图片为例大致讲解：
1.transform对JS的能力要求高些，页面加载完毕后，给每个B区域对应的DIV设置translate3d的Y轴值，值为B对应div离顶部的高度。
2.当开始滚动起来后，Y轴值为原来值减去滚动条滚动距离值
3.当Y轴值小于0的时候，锁死为0，就自动吸顶了
4.当待置顶DIV的translate3d Y轴值小于DIV的高度时，已置顶DIV的Y轴值开始变为负数，这样会有一个顶出的效果
5.已置顶DIV被待置顶DIV顶出一定距离（一般两倍DIV高度），取消translate3d属性，
6.回滚反向计算即可

需求二：滚动过程中 C不吸顶，B吸顶，A一直吸顶 B在A下面
这个需求vantUI（v2.9.3）目前解决不了,采用translate太复杂，决定采用CSS3新属性position: sticky。
  如果是2018年使用该属性兼容性还是个问题，但来到2020年下半年，对大部分生产环境都不是问题。博主测试的IOS11和安卓小米 华为 都支持该属性。

![image.png](https://upload-images.jianshu.io/upload_images/9645550-9d34620fe7edf645.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
[https://www.caniuse.com/#search=sticky](https://www.caniuse.com/#search=sticky)

博主的建议是：如果是公司APP内嵌H5页面可以使用该属性，如果纯H5页面 比如微信打开，建议translate.

position: sticky;字面意思就是粘性定位。
可以粘顶部也可以粘底部 右侧 左侧。
所以除了position: sticky;还要给一个定位值，比如top:0px或者right:0px就可以了 非常简单。
比如直接给Bdiv设置下面样式就行
![image.png](https://upload-images.jianshu.io/upload_images/9645550-a271f31ae02c2406.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实际使用要注意下面几点：
1.如果想兼容IOS12 IOS11 必须带position: -webkit-sticky;而且要在样式表里写，不能写在style里。
2.容器相关。只有要移除容器范畴才起作用。比如body的height不要设置100%，height：100%表示所有元素一直在屏幕范围



