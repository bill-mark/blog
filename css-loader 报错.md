css-loader：版本 5.0
报错1：options has an unknown property 'localIdentName'. These properties are valid
5.0后的版本module后面不是一个布尔值而是一个对象配置参数

比如vue-loader中文官网的配置 [地址](https://vue-loader.vuejs.org/zh/guide/css-modules.html)
![image.png](https://upload-images.jianshu.io/upload_images/9645550-297731858f13816a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


正确写法如下：
```
 {
                    //解析器的执行顺序是从下往上(先css-loader再vue-style-loader)
                    test: /\.css$/,
                    use: [
                        process.env.NODE_ENV !== 'production'
                            ? 'vue-style-loader'
                            : MiniCssExtractPlugin.loader,  //prod CSS被提取出来打包
                        {
                            loader: 'css-loader',
                            options: {
                                modules:{
                                    auto:false,//modules 开关,移动端多页面模式关闭class hash命名
                                    localIdentName: '[local]_[hash:base64:8]',// 自定义生成的类名
                                }
                            }
                        }
                    ]
                },
```