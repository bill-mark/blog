vue-cli@3警告!

如果按照element官网对vue-cli3的适配,会修改app.vue 文件!除非你是在初始化项目的时候,否则项目已经开始了 不要这么做.

      先说作者自己按需引入elementui的好处,全局引入是超过1M的,按需引入后不到400K,作者按需引入用到的组件有分页,日期选择器,tree选择器,input.

第一步:

     项目目录执行 npm install babel-plugin-component –D

第二步:修改.babelrc

这是Vue脚手架项目按需引入elementui核心的地方,因为大部分新手不会babel配置.

如果直接复制粘贴官网提供的配置文件肯定会报错,因为你把脚手架默认的配置给覆盖掉了,正确的做法是做整合,babelrc应该修改为:

{

  "presets": [

    ["env", {

      "modules": false,

      "targets": {

        "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]

      }

    }],

    ["es2015", { "modules": false }],

    "stage-2",



  ],

  "plugins": [

      "transform-vue-jsx",

      "transform-runtime",

      [

            "component",

            {

              "libraryName": "element-ui",

              "styleLibraryName": "theme-chalk"

            }

      ]

  ]

}




如果elementui版本是1.X,则styleLibraryName的值要修改为theme-default
第三步:按需引入组件

比如要按需引入button,则为

  import Vue from 'vue' 

  import App from './App' 

  import router from './router'

  import 'element-ui/lib/theme-chalk/index.css' 

import { Button } from 'element-ui'

   Vue.use(Button)

  贴张作者个人引入的例子:




第四步.错误处理

如果报错Module build failed: Error: Couldn't find preset "es2015" relative to directory

 则执行npm install babel-preset-es2015 --save-dev

报错These dependencies were not found:deepmerge,resize-observer-polyfill ,throttle-debounce/debounce

则npm install --save deepmerge resize-observer-polyfill throttle-debounce/debounce

在线例子:这里有个作者写的vue-cli按需引入elementui的例子可以参考

GitHub - bill-mark/elementui-demand: vue 2.X和elementui 2.X搭配使用时,按需引入.