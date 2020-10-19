 随着三大框架(vue,react,angular)的流行,导致前端开发者越来越少的接触dom操作,慢慢忘了有些需求dom才是最优解.

   需求:子组件滚动时执行对应函数

   如果直接在子组件mounted里addEventListener,
   只能监听到父组件的滚动,子组件监听不到

   解决方案
           查看addEventListener的文档得知:
         1.  子组件mounted里addEventListener的时候,第三参数要设置为true,因为子组件滚动是捕获事件,父组件滚动是冒泡事件,addEventListener第三参数默认false只监听冒泡事件,所以子组件的捕获事件默认监听不到,改为true才可以.
``` 
window.addEventListener('scroll',this.handleScroll,true) 
````

 2.这时候子组件和父组件的滚动都能触发事件,区别前者是捕获后者是冒泡,通过查阅event的文档得知,冒泡事件event.bubbles值是true,捕获事件的值是false.所以通过addEventListener的回调函数判断event的bubbles值即可区分是父组件滚动还是子组件滚动.

![event属性](https://upload-images.jianshu.io/upload_images/9645550-5a126c8bef8d4241.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

回调函数:
 ```
handleScroll (event) {
    if(!event.bubbles){  //子组件滚动
      
    }else{  //父组件滚动
      
    }
  },
```
   3.记得销毁组件的时候移除监听事件
```
destroyed(){
  window.removeEventListener('scroll',this.handleScroll)
},
```
   