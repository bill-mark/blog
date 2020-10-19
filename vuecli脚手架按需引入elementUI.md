vue-cli更新到第三版,第二版的按需引入已经不适合了,[第二版引入传送地址]([https://www.jianshu.com/p/40da0ba35ac1](https://www.jianshu.com/p/40da0ba35ac1)
)

直奔主题
vue-cli3引入elementui其实有三种方式:

1.全局引入
  项目package.json同级目录
  执行 vue add element
  后面有两个选项 全部默认 回车就行

2.按需引入插件版   (适合初始化项目的时候)
  项目package.json同级目录
  执行vue add element
然后
  ```
? How do you want to import Element? (Use arrow keys)
❯ Fully import 
  Import on demand 

```
选择import on demand
下面一项是语言 默认即可(zh-cn)

会修改app.vue 同时src目录下会新增plugins文件夹,该文件夹下有个element.js文件
![image.png](https://upload-images.jianshu.io/upload_images/9645550-feabde00579f7d0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开该文件
```
import Vue from 'vue'
import { Button } from 'element-ui'

Vue.use(Button)
```
已经有个按需引入button的例子了,想引入的组件按照import XXX from 'element-ui' 这种写法就行,也可以放在一个{}里.
比如按需引入Button和Select 
```
import Vue from 'vue'
import { Button,Select } from 'element-ui'

Vue.use(Button)
Vue.use(Select)
```
当引入JS组件,记得要绑定到vue原型上,比如Message
```
import {Message} from 'element-ui'
Vue.use(Message)
Vue.prototype.$message = Message;
Message.closeAll()
```

3.按需引入手动版 (适合项目开发中)
通过vue add的方式引入会修改项目的文件,比如app.vue还会新增plugins目录等,侵入式比较强.
原理上vue add模式添加的插件都可以通过npm的方式实现,基于这个原理和参考vue-cli2的按需引入方式,有如下方法,不会修改项目文件.
项目package.json同级目录下执行:
npm install  element-ui -S
npm install babel-plugin-component -save-dev
npm install vue-cli-plugin-element -save-dev

其实就是把vue add element安装的插件手动安装一遍,但是没有调用文件生成器,所以不会修改现有项目文件

安装好之后 ,打开babel.config.js
如果没有plugins配置选项,复制粘贴下面配置(和方法2生成的配置一样)
```
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset'
  ],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```
如果babel.config.js里有了其他plugins的配置信息,不能直接覆盖,给plugins新增一个配置
```
[
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
```



在main.js中配置需要按需引入的组件
比如按需引入Message组件
```
import Vue from 'vue'
import App from './App.vue'
import router from './router'

import {Message} from 'element-ui'
Vue.use(Message)
Vue.prototype.$message = Message;
Message.closeAll()

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
```
在app.vue中就可以直接使用Message了
```
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
      <div @click="test">message</div>
    </div>
    <router-view/>
  </div>
</template>

<script>
  export default{
    methods:{
      test(){
        this.$message('这是一条消息提示')
      }
    }
  }
</script>
```

