#### 传统弹窗插件不足:

1.传统弹窗插件是按照vue官网文档组件的开发范式,把弹窗插件当成一个子组件开发,通过emit向父组件通信,这种做法一般情况都能胜任.
    但是当一个父组件里的孙子组件需要弹窗的话就非常麻烦,要么孙子组件通过emit通知子组件,子组件再$emit通知父组件.或者vuex,eventbus.体验很糟糕

2.确认消息弹窗和消息提示弹窗要想使用一个组件来实现非常麻烦,要多传一个参数,回调也是.

#### 为什么还要造轮子:

弹窗插件挺多的,比较成熟好用的有elemetui的,但是elementUI的弹窗插件,确认消息和消息提示是两个插件,大部分业务是不需要分这么细节的,本着代码能缩就缩的原则开发了一个插件有消息提示和确认消息两个功能,降低代码大小.

![消息提示](https://upload-images.jianshu.io/upload_images/9645550-13fac1cf363f539e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![确认消息](https://upload-images.jianshu.io/upload_images/9645550-21ce9e225a157663.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


调用方式为:
```
this.$toast({
          showCancle:true,
          dialogname:'提示',
          alert_text:'这是内容'
        })
        .then( ()=>{
          console.log('promise then')
        })
        .catch( ()=>{
          console.log('promise catch')
        })
```


#### 开发步骤:

1.初始化一个vue-cli脚手架或者在你当前项目中,assets文件夹下新建toasttemplate.vue和toast.js文件,

2.toasttemplate.vue

这是弹窗组件所在的文件,大概思路是父div包两个子div,一个子div是cover层占满屏幕,另一个子div是内容层,z-index最大.和标准组件稍微不一样的是当点击确定或者取消的时候,调用的是toast.js加在原型上的函数而不是$emit
```
<!-- 弹窗组件 -->
<template>
  <div class="tanchuang_wrap" v-if="isShow">
     <div class="dialog_cover"  @click="closeMyself"></div>

     <transition name="drop">
     <div class="dialog_content" v-if="isShow">
         <div class="alert_title">
             <div class="alert_title_left">{{dialogname}}</div>
             <div class="alert_title_right" @click="closeMyself"></div>
         </div>
         
        <div style="padding: 10px 15px;;text-align:left;
                    font-size:14px;color:#606266">
              {{alert_text}}
        </div>

         <div class="down" > 
             <div v-if="showCancle" class="down_button_cancle" @click="closeMyself">取消</div>
             <div class="down_button" @click="besure">确定</div>
         </div>
     </div>
     </transition>
  </div>
</template>

<script>
export default {
data() {
  return {
    isShow: false,
    showCancle:false,
    dialogname:null,
    alert_text:'',
  }    
},
methods: {
  show(params){  //初始化参数
     let { showCancle,dialogname,alert_text } = params
     this.showCancle = showCancle
     this.dialogname = dialogname
     this.alert_text = alert_text

     this.isShow = true
  },
  closeMyself () {
     this.isShow = false
     this.callBack(false)
  },
  besure(){  //确定按钮
     this.isShow = false
     this.callBack(true)
  },
}
}
</script>

<style lang="scss" scoped>
.drop-enter-active {
  transition: all .5s ease;
}
.drop-leave-active {
  transition: all .5s ease;
}
.drop-enter {
  transform: translateY(-500px);
}
.drop-leave {
  transform: translateY(-500px);
}


.tanchuang_wrap{
  .dialog_cover {
    background: #000;
    opacity: 0.3 ;
    position: fixed;
    z-index: 35;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  .dialog_content {
    width: 422px;
    position: fixed;
    overflow: auto;
    background: #fff;
    top: 15%;
    left: 50%;
    margin-left: -211px;
    z-index: 40;
    line-height: 1.6;
    border-radius:4px;
    padding-bottom:10px;
    .alert_title{
        background:white;
        width:100%;
        height:43px;
        overflow: hidden;
        .alert_title_left{
           float:left;
           margin-left: 15px;
           margin-top: 15px;
           font-size:18px;
           color:#303133;
           line-height: 1;
        }
        .alert_title_right{
          float:right;
          width:12px;
          height: 12px;
          margin-top: 20px;
          margin-right: 20px;
          background-image: url('../../static/关闭.png');
          background-repeat: no-repeat;
          background-size: 12px 12px;
          cursor: pointer;
        }
        .right:hover{
          color: #4fc08d;
        }
    }
    .down{
      padding: 5px 15px 0;
      display: flex;
      flex-direction:row-reverse;
      .down_button{
         width: 55px;
         height: 30px;
         font-size: 12px;
         cursor: pointer;
         background:#2389ff;
         border-radius:3px;
         text-align: center;
         line-height: 30px;
         color: white;
         font-weight: 500;

      }
      .down_button_cancle{
         margin-left:15px;
         width: 55px;
         height: 30px;
         font-size: 12px;
         cursor: pointer;
         background:#fff;
         border:1px solid #CCCCCC;
         border-radius:3px;
         text-align: center;
         line-height: 30px;
         color: #999;
         font-weight: 500;
      }
    }
  }
}
</style>

```
3.开发toast.js
   这是比较重要的地方,思路为:

   a.创建一个函数,根据参数bool值，返回存贮promise结果变量的resolve或reject属性,这个函数会被toasttemplate.vue来调用

   b.根据vue官方文档插件开发规范,创建一个对象,用来对外暴露

   c.在这个对象里使用vue.extend把toasttemplate.vue构造成一个vue类,再由这个类生成的对象(dom)放在dom上使用

   d.在对象里要返回一个promise对象,promise的resolve和reject存在一个变量中,由a步骤中的函数调用,并把这个函数放在新建vue类的原型上
```
import Toasttemplate from './toasttemplate.vue'
function defaultCallBack (action){
	if(!action) currentMsg.reject()
	currentMsg.resolve()
}

let Toast = {}
let currentMsg = null

Toast.install = function(Vue,options={}){
 	const VueToast = Vue.extend(Toasttemplate)  //创建模板
    let toast = null
    VueToast.prototype.callBack = defaultCallBack

 	Vue.prototype.$toast = (params) =>{
		if(!toast){
			toast = new VueToast().$mount()  //创建实例
			document.body.appendChild(toast.$el)  //挂载实例
		}
		toast.show(params)
 		
 		return new Promise((resolve,reject) => {
 			currentMsg = {resolve,reject}
 		})
 	}
 }



export default Toast
```

4.注册
在main.js中
import Toast from './assets/Toast'
Vue.use(Toast)

5.使用
```
this.$toast({
          showCancle:true,//true->确认消息弹窗,false->消息提示弹窗
          dialogname:'提示',//弹窗的标题
          alert_text:'这是内容'//弹窗的内容
        })
        .then( ()=>{
          console.log('promise then')
        })
        .catch( ()=>{
          console.log('promise catch')
        })
```

到第五步就可以直接在项目里用了，下面是制作成npm插件
6.制作成npm插件
下载webpack-simple,然后初始化一个项目
  在src目录下面新建文件夹lib,将上述文件和用到的静态资源拷贝进去
![目录结构](https://upload-images.jianshu.io/upload_images/9645550-3d8bb2dc65c3def6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 修改webpack.config.js的entry和ouput

![webpack.config.js](https://upload-images.jianshu.io/upload_images/9645550-25d8f58a249e2993.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

package.js新增main属性,路径为打包后的入口js路径

![package.json](https://upload-images.jianshu.io/upload_images/9645550-345ee602d7800c4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改index.html
引入打包后的文件

![index.html](https://upload-images.jianshu.io/upload_images/9645550-1cf59f4139e16bbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


发布到npm上即可


      
        

       


##总结
   该插件已经被作者制作成npm插件,GitHub地址:https://github.com/bill-mark/vue-toast-plus