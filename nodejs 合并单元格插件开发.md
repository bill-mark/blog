文章分 两个部分:

前面部分:介绍合并单元格插件的开发

后面部分:介绍如何发布到npm上,以及如何更新

前面部分:

项目中常常遇到表格合并单元格的需求,以往都是直接安装一套UI插件来 解决,但这带来 两个问题:

1.不够定制化 ,尤其是样式冲突  2.UI插件体积比较大,有点为了芝麻装了个西瓜的感觉

代码逻辑:

1.给表格设置ID

2.通过ID拿到表格dom,然后双for循环判断,如果当前单元格和同列下一行的单元格内容相等,

下一行的单元格的display设置为none,不相等当前单元格和下一个单元格display设置为table_cell

3.当前单元格的rowspan在第二个人for循环里要依次递增




后面部分:

1.创建一个文件夹merge-tablecell,命令行进到文件夹里 ,执行npm init

2.一直回车下去,出现问号输入yes

3.打开package.json  

    查看main属性的值 如果是index.js 就在package.json同级新建个index.js,是main.js就新建个main.js

    如果没有main属性,需要自己建一个main属性,值为打包后的入口地址或者直接项目入口地址也行

4.在 建一个README.md文件 用来说明插件用法




5.打开index.js

把函数放进去,然后 通过export暴露出来,就和平时开发本地js库文件一样




6.这时候回到命令行,

如果有npm账号,输入npm login登录

没有的话,输入npm adduser  表示注册 账号

要注意node不能是淘宝镜像,是的话要改回来.

依次填入 名字,密码,邮箱即可

7.输入npm publish 就发布啦,记得package.json的private要改为false

8.更新插件

命令也是npm publish

如果插件有更新,比如要修改index.js.修改完之后 还要修改oackage.json和package-lock.json里的version