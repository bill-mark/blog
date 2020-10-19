第一步 登录
微信扫码登录微信开发者平台,扫码地址:微信公众平台

确定网页授权权限已开:点击左侧菜单栏 开发-接口权限, 找到网页授权功能选项,没有权限需要申请开通.






第二步 修改白名单
1.点击菜单 开发-基本配置


2.选择ip白名单-查看




在弹窗中选择修改,把后端服务器IP地址(后端接口地址)和前端服务器IP地址(网站网址)放上去




3.保存修改

第三步 填写网页授权域名
1.依次打开公众号设置-功能设置-网页授权域名设置,会出现一个弹窗

2.在输入框中输入前端线上地址,不包含http头

比如前端URL是 http://cc.wechatyanzheng.com

那就填写cc.wechatyanzheng.com.

注意一点:如果你是在开发测试阶段,是需要把测试网址绑定域名发布出去的,上面就填写你的对外测试域名




3.在弹窗中,会提示你下载一个验证文本txt.把它下载下来放在你的前端服务器路径第一级目录上,通俗点讲就是和你的入口文件放在同级,比如入口文件是index.html,那就把这个文件放在index.html的同级

第四步:下载调试工具
1.打开微信公众平台技术文档-微信web开发组工具-下载地址

网址为:微信公众平台




第五步:配置授权回调地址微信公众平台
1.该地址的功能逻辑

这个网址是当用户点击某个按钮,微信打开的地址.比如点击授权绑定可以跳到这个地址,或者打开某个链接也需要先到这个地址拿到openID

2.配置地址

地址结构:微信验证地址接口+公众号ID+业务地址(你希望用户看到的页面url,urlEncode格式包括http头)

填写规范为:将中文替换成对应的参数即可

https://open.weixin.qq.com/connect/oauth2/authorize?appid=公众号ID&redirect_uri=业务地址&response_type=code&scope=基本授权/完整授权&state=有MD5验证填写#wechat_redirect

比如:

https://open.weixin.qq.com/connect/oauth2/authorize?appid=111222233&redirect_uri=http%3a%2f%2fcc.wechatyanzheng.com&response_type=code&scope=snsapi&state=123#wechat_redirect

第六步:测试能否拿到返回的code
假如你的授权回调地址是正确的,点开这个地址微信验证成功后跳转到你的业务地址并附加参数,一个参数是code一个参数是state(授权地址有就返回没有不返回)

我们先验证能不能拿到code,再进行下一步

1.把前端代码发布到测试服并绑定好域名,这个域名就是第五步中redirect_uri指向的域名,同时确认一级目录存有第三步中的验证文本

2.打开下载的微信开发者工具

登录你的微信号,确定你的微信号已经关注了要开发的公众号.

3.在开发者工具的地址栏中放入授权回调域名




4.点击回车,地址正确会返回code




第七步.通过code拿到openID
这一步因为要传公众号的密码,官方和作者都建议由后端进行获取,当然如果团队小,进度第一,前端获取也是挺简单的

1.跨域

直接访问会报跨域错误,如果你是用Vue或react开发,需要在脚手架配置个https://api.weixin.qq.com代理

2.先去基本配置页面拿到开发者密码,即appsercret




3.访问openid获取接口:

https://api.weixin.qq.com/sns/oauth2/access_token?appid=公众号ID&secret=公众号appsercret&code=URL返回的code值&grant_type=authorization_code

将对应中文替换成相应参数即可

在微信开发组工具中进行调试,成功会返回openid




小结
至此,我们就拿到openID了  谢谢大家的阅读

有空可以去下方GitHub地址帮忙点个星星 谢谢

GitHub - bill-mark/wechat-get-openid: 微信网页授权如何获得openID

