######需求背景：
最近维护一个老项目，没有前端交接，文档只有项目启动和打包说明，配合的后端也是半路接手只愿提供新接口的文档。
项目甲方不能远程调试，只能去甲方场地连甲方专用网络才能访问，老项目的框架是express+ejs学习价值不大。
接口返回值基本嵌套在4层以上，返回的数据非常多和杂。
这个时候如果对这个项目进行维护就非常头疼，经常花很多时间在接口和层层嵌套的回调函数上。
被折磨两周后决定开发一个谷歌浏览器插件，能对页面接口做归纳还能导出接口，然后就可以根据导出的接口整理到在线接口服务比如rap上，这样老接口有了完整的接口文档，再进行维护就非常简单了。

######插件的功能：
可以导出页面发生的异步请求接口

####开发步骤：
1.  新建一个目录apiman，在目录下新建文件manifest.json  manifest.json类似前端项目的package.json用来声明项目的名称，类型，权限等

2.mainifest.json配置介绍：
![image.png](https://upload-images.jianshu.io/upload_images/9645550-fb9cb2b980d5be4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
manifest_version表示对应谷歌插件sdk版本，现在是2，
name:插件名
version：插件版本，自己定义
description：拓展程序页面的插件介绍
![image.png](https://upload-images.jianshu.io/upload_images/9645550-05d831f2edd1ae78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
browser_action：地址栏右侧插件图标行为描述
default_popup:左键点击图标显示的页面
default_title：鼠标悬浮提示内容
default_icon:图标地址
devtools_page:如果要在devtools新增一个pannel需要配置此页面
![image.png](https://upload-images.jianshu.io/upload_images/9645550-a42f6e6b8568b31f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
permissions：插件权限，
background：通信页面，类似vuex,redux，插件有好多个页面比如devtools页面，插件的default_popup，这些页面好多不能直接通信，需要借助background配置的页面作为中转。
大部分插件还需要一个web_accessible_resources配置，是用来指定注入JS的文件位置，注入JS也是通信的一种实现方式，由于apiman插件目前只有一个交互按钮，暂时用不到

3.目录搭建
在主目录下新建icon目录和JS目录，根据manifest.json配置，在主目录下新建browser.html，devtools.html。在JS目录下新建background.js，在icon目录下放入文件logo.png。
![image.png](https://upload-images.jianshu.io/upload_images/9645550-8e773d02b8f2aa4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
接下来将会按照manifest.json里引入的文件依赖关系进行挨个开发，比如manifest.json从上至下先引用了browser.html,就先开发browser.html,browser.html里又引用了browser.js，会接着开发browser.js直至没有引用文件。然后在回到manifest.json介绍下一个文件
4.根据manifest.json的配置顺序开发browser.html
```
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
</head>
<body>
  <div style="font-size:12px;width: 170px;">1.打开开发者工具</div>
  <div style="font-size:12px;">2.选择顶部最右侧面板ApiMan</div>
  <button id="changeColor">背景变红</button>
  <script src="js/browser.js"></script>
</body>
</html>
```
![image.png](https://upload-images.jianshu.io/upload_images/9645550-a5d2e40923d23dca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

browser.html即插件的主页，和平时写的HTML没有区别，需要注意的是谷歌插件不能在HTML里写JS，用到JS的地方要引入JS文件。如图，要实现一个点击按钮，页面背景变红色的功能，要在browser.html里引入browser.js

5.开发browser.html引入的browser.js
```
let changeColor = document.getElementById('changeColor');
  
  changeColor.onclick = function(element) {
  	
    let color = "red";
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
      chrome.tabs.executeScript(
          tabs[0].id,
          {code: 'document.body.style.backgroundColor = "' + color + '";'});
    });
  };
```
这是实现插件主页点击变红色按钮页面变色的代码，大概意思是绑定ID，监听click事件。谷歌插件JS和HTML不是一一对应的，监听到click事件后，要通过 chrome.tabs.query接口查找到页面ID，再进行dom操作才行.

6.回到manifest.json,按照配置顺序开发devtools.html
```
<html>
<head></head>
<body>
	<script type="text/javascript" src="js/devtools.js"></script>
</body>
</html>
```
谷歌插件要想运行JS必须要依赖一个HTML，background.js会自动创建一个background.html,所以在这里devtools.html只充当一个桥接的作用，里面的dom不会变成panel里的内容，起作用的是里面引用的devtools.js脚本代码,

7.开发devools.html引用的devtools.js
如果想开发一个devtools上的插件，需要用到devtools.js
```
chrome.devtools.panels.create('ApiMan', '', 'panel.html', function(panel)
{

});
```
chrome.devtools.panels.create用来创建panel,参数依次为：panel名，panel图标（没用），panel对应的HTML，成功回调。
写到这，当打开一个页面调出控制台就能看到自己定义的panel了
![image.png](https://upload-images.jianshu.io/upload_images/9645550-806b915501ecbf67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

8.开发devtools.js引用的panel.html
```
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<style type="text/css">
		pre {outline: 1px solid #ccc; }
	</style>
</head>
<body>
  <div>第一次打开本面板需要刷新下页面，以获得页面初始化的异步请求 </div>
  <br>
  <button id="export_json">导出</button>

  <div style="height: 50px;line-height: 50px;">获得的api列表</div>
  <pre id="results"></pre>


  <script  src="./js/panel.js"></script>
</body>
</html>
```
![image.png](https://upload-images.jianshu.io/upload_images/9645550-91c19059e4008fc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这段代码和传统前端开发没什么区别，同样注意的是不能写JS只能引入JS文件，代码里写的HTML会变成pannel页面显示出来

9.开发panel.html引用的panel.js
在pannel.html里引入pannel.js来执行相应的JS操作
apiman的pannel.js代码比较多我们不展示了，我们在这里额外拓展一个需求：在pannel里点击导出按钮后，执行background.js里的弹窗函数，来看看是怎么实现的
pannel.js中：
```
let exportjson = document.getElementById('export_json');
  
exportjson.onclick = function(element) {
    var bg = chrome.extension.getBackgroundPage();
    bg.test(); // 访问bg的函数 
    //export_file(entries)
};
```
可以看出pannel.js监听了pannel.html中ID为export_json的元素，当export_json触发点击事件后，通过chrome.extension.getBackgroundPage引入background.js到bg变量上，然后bg.test()来触发background.js里的函数

10.panel.js引用了background.js，所以来开发他
```
function test(){
  alert('this is background test ')
}
```

至此，我们的谷歌插件apiman算是初步完成了,[插件github地址]([https://github.com/bill-mark/chromeExtensions-apiman](https://github.com/bill-mark/chromeExtensions-apiman)
)。
使用例子
![image.png](https://upload-images.jianshu.io/upload_images/9645550-6b802631b009cde9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

后续该插件还会增加定制化导出，在线生成文档等，敬请期待
