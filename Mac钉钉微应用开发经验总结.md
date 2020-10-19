目前遇到的问题和解决方案如下:
问题1.Mac钉钉微应用调试环境搭建
首先在钉钉管理后台成为开发者
![image.png](https://upload-images.jianshu.io/upload_images/9645550-e6c235ac580947dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一般用Mac的coder基本用iPhone,博主开发的时候,钉钉微应用开发包还没iPhone版,所以只能用Android机器调试

解决方案一:
1.在[https://ding-doc.dingtalk.com/doc#/kn6zg7/zunrdk](https://ding-doc.dingtalk.com/doc#/kn6zg7/zunrdk)下载钉钉开发包安装到安卓手机上
安卓手机+chrome
博主的Mac只有TypeC接口,有两个安卓测试机,一个三星TypeC口,一个OPPO micro口.
第一次尝试:TypeC手机安装钉钉开发包连接电脑,三星手机安装开发包一直显示应用未安装,卒!
第二次尝试:OPPO手机安装开发包成功!
2.手机打开 开发者模式,USB调试开关打开
3.登录钉钉Dev版,设置-通用-开发者选项-微应用调试打开
4.手机连接电脑,打开电脑谷歌浏览器,地址栏输入chrome://inspect回车
5.![image.png](https://upload-images.jianshu.io/upload_images/9645550-b83933ec3f5225cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果你看到上图红圈中的图标说明手机连接电脑成功并被检测到了!
如果没有看到对应图标,打开页面控制,控制台右上角有三个点标志的设置,点击设置.
![image.png](https://upload-images.jianshu.io/upload_images/9645550-26df1e28f7bc2646.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点开设置后按照下图选择remote device这样设备就出来了
![image.png](https://upload-images.jianshu.io/upload_images/9645550-635a29ce51101911.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
还不出来的原因博主碰到是数据线坏了,只能充电不能传输数据(折腾一天才找到这个原因....)
6.本地前端脚手架启动,要监听到内网IP端口上,
  比如vue/cli3 run server之后如图所示
![image.png](https://upload-images.jianshu.io/upload_images/9645550-db5213a5abcc016a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们用图片中network后面的地址
7.![image.png](https://upload-images.jianshu.io/upload_images/9645550-999fc66178a8ce0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进去钉钉管理后台,新建微应用,微应用的应用首页链接填前端脚手架监听的地址
然后创建微应用
8.这时钉钉APP上应该能看到这个微应用,打开它
9.![image.png](https://upload-images.jianshu.io/upload_images/9645550-2e5e2e7ebe477040.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当地点开钉钉里的微应用的时候,浏览器页面会出现如图所示变化

点击红框中的inspect,会弹窗出现模拟器!就可以愉快的开发了
![image.png](https://upload-images.jianshu.io/upload_images/9645550-3af82cf5301a2189.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解决方案二:
电脑内存大,16G或32G更推荐这个
安卓模拟器+Chrome
1.下周并安装genymotion,下载地址[http://www.genymotion.net/](http://www.genymotion.net/)
2.启动genymotion
![image.png](https://upload-images.jianshu.io/upload_images/9645550-667857e2c0c35c33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.随便建个安卓模拟器,安卓版本7,博主安卓版本8模拟器链接不到网,如果模拟器运行后找不到网络,直接新建模拟器.解决genymotion模拟器断网问题比较玄学,不推荐
4.在[https://ding-doc.dingtalk.com/doc#/kn6zg7/zunrdk](https://ding-doc.dingtalk.com/doc#/kn6zg7/zunrdk)
下载钉钉开发包,拖进模拟器自动安装
5.模拟器设置里打开 开发者模式 ,USB调试模式
6.pc谷歌打开chrome://inspect 
下面的步骤和方案一相同,参考方案一即可
至此Mac调试环境搭建完成!

问题二:横屏打开页面
首先:
使用dd.biz.util.openLink打开页面,在URL后面拼接dd_orientation=landscape,直接用脚手架的路由会不生效.
其次:
如果安卓生效,iOS不生效,需要下载钉钉内网穿透软件[https://ding-doc.dingtalk.com/doc#/kn6zg7/hb7000](https://ding-doc.dingtalk.com/doc#/kn6zg7/hb7000)
,把本地端口映射到域名上,vue脚手架需要在vue.config.js里devServer新增配置disableHostCheck: true,用来跳过host检查
如果iOS还不能横屏,博主就遇到过.iOS升级没起作用,最后把钉钉卸载重装可以了.