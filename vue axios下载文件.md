导语:
   当你点进这篇文章说明你的http基础很烂,该去补http的知识了.

  网上搜了一堆根本就不好使,都是JQ时代的教程,mvvm框架的教程质量很差

先上干货

需求:点击DIV,下载Excel文件

url地址为:http:www.xxx.com/api/download

参数:id:11,time:2018.9.1


特征:参数少,不超过浏览器get长度限制

思路:借助a标签的原生功能,写成div嵌套a标签,a标签的href指向URL.







注意参数的拼接,URL后面跟?,参数之间跟&,记得跨域


如果参数非常多,超过了get的长度限制,只能用post

后端正常水平:

post把参数传给后端接口后,让后端返回文件的下载地址,再按照方法1中用window.location.href打开这个地址即可.





后端是萌新的话,只给你个下载接口,按照下面方法来:

多讲一点,文件属于流文件不是json,所以要改响应类型responseType为arraybuffer而不是改header.拿到二进制文件还需要按照服务器响应头的数据类型转换.

第一步:写好请求


第二步:访问下后端提供的下载接口,复制接口response headers的content-type放在上图中type的字符串中





至此点击div就能实现下载了
这种方法会有个问题,当接口报错,后端返回的error是个json前端看到的会是乱码,

因为已经被浏览器处理成文件流了.

第一种办法,报错后端直接返回http状态码4XX,带error

第二种办法,http状态码还是200,需要和后端在响应头约定下报错格式.

比如后端在header里新增个errcode属性和error_content属性,如果errcode不等于undefined,中断执行,弹窗error-contente内容

出错排查建议:

1.检测请求的参数是不是json格式

2.后端接口的响应类型是不是文件格式

3.有没有跨域,为了直观教程中写的是完整URL,实际开发是写代理接口的

4.下载的文件名编码是后端设置的,文件名乱码也是后端的问题,可以试下在ajax函数里设置文件名编码

headers:{'filename':'utf-8'},

如果有帮助可以去github点个start   GitHub - bill-mark/axios-post-file-excel: axios post方式下载文件

谢谢

