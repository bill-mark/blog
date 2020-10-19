前言：
   VUE弹窗动画建议不要参考官网封装好的的transition，本来很简单的一个需要，越看越晕。

先上源码，看不懂再往下看：
```
<sharewindow :isShow="show_share" @close="close_sharewindow"></sharewindow>
```

```
<template>
  <div class="share_dialog" @click.stop @touchmove.prevent v-if="isShow">
       <div class="share_cover" @click="closeMyself"></div>
       <div class="share_content" >

       </div>
  </div>
</template>

<script>
export default {
    data(){
       return {
            
       }
    },
    props:{
         isShow: false,
         title:'',
         content:'',
    },
    methods:{
        closeMyself() {
            this.$emit('close')
        },
    }
}
</script>
<style scoped lang="less">
.share_dialog{
    position: relative;
    .share_cover{
        position: fixed;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.49);
        top: 0;
        left: 0;
        z-index: 999;
    }
    .share_content{
        position: fixed;
        bottom: 0;
        background-color:white ;
        width: 100%;
        height: 5.04rem;
        z-index: 999;
        animation:window-open 0.5s 1;
    }

    @keyframes window-open
    {
       0% {
           bottom:-5.5rem;
       }
       100% {
           bottom:0;
       }
    }
}
</style>
```

开发流程：
1.先写好一个不带动画的弹窗组件，share_cover为遮罩DIV，share_content为内容DIV
2.（核心）
   弹窗动画，我们要给内容DIV的CSS加个animation属性。这里我们给animation设置了三个值：动画CSS名，时间，动画播放次数
```
animation:window-open 0.5s 1;
```
3.动画CSS名，按照组件功能命名就行，动画有什么效果在@keyframes里面配置
```
@keyframes window-open
    {
       0% {
           bottom:-5.5rem;
       }
       100% {
           bottom:0;
       }
    }
```
keyframes后面要跟动画名，然后0%表示动画开始的DIV样式，会覆盖掉animation所在DIV的属性。比如我们这里0%的时候bottom设置为-5.5rem,那么
share_content里面的bottom会开始会被替换成-5.5rem.

4. 100%表示动画结束时，对应DIV的相应样式。要注意的一点是，动画播放完毕后，动画里的样式将不会再影响对应DIV的样式，所以对应DIV也要把相应CSS属性加上，比如这里是share_content{ bottom: 0;}
![image.png](https://upload-images.jianshu.io/upload_images/9645550-169734e31a816bc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
