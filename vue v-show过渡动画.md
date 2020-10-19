需求:
一个div,v-show绑定的值为true时一个动画,为false时又一个动画
以放大缩小动画为例

遇到的问题:
v-show的本质是改变display的值,display的值改变会引起HTML画布的重绘,会导致动画只能完成一半,要么放大不能缩小,要么缩小不能放大.

解决思路
不采用display的方式改为绝对定位+visibility
即v-show改为切换div的class名
这个div有三个class可选,但某一状态只展示一个class,一个不带动画的初始class,观察变量为true时的class,为false时的class

第一步
class绑定到data的对象上
```
<div :class="s_obj"></div>

data:{
  x:false, //动画状态变量
  s_obj:{
        init_class:true,
        show_in:false,
        show_out:false
      }
}
```
第二步
这步参考display时候的写法就好
比如当X为true的时候,需要放大
```
.show_in{
    animation: go_in 1s;
    opacity: 1;
    visibility: visible;
    z-index: 20;
    position: absolute;
    width:100px;
    height:100px;
}
 @keyframes go_in
  {
     0% {opacity: 0; transform: scale(0);}
     100%{opacity: 1; transform: scale(1);}

  }
```
同理,当X为false时候需要缩小
```
.show_out{
    animation: go_out 1s;
    opacity: 0;
    visibility: hidden;
    z-index: 0;
    position: absolute;
    width:100px;
    height:100px;
}
@keyframes go_out
  {
     0% {opacity: 1; transform: scale(1);visibility: visible;}
     100%{opacity: 0; transform: scale(0);}

  }
```
第三步
如果这时候写根据X的值修改class的类名会有一个问题,比如刚进入页面,X为false,则div为
```
<div class="show_out"></div>
```
进入页面会有一个缩小的动画,这时候需要给div一个不带动画的初始class,除了没有动画其他值和shou_out一样
```
.inint_class{
    opacity: 0;
    visibility: hidden;
    z-index: 0;
    position: absolute;
    width:100px;
    height:100px;
}
```

第四步
经过第三步div的初始class为init_class,这个时候我们需要watch变量X,当X为真时class为show_in,为假时calss为show_out即可
```
watch:{
X(){if(this.show_demo){
           this.s_obj = {
              init_class:false,
              show_in:true,
              show_out:false
           }
           else{
              this.s_obj = {
                  init_class:false,
                  show_in:false,
                  show_out:true
              }
           }
}
}
```
至此,过渡动画特效完成!也可以用定时器的方式,本质差不多