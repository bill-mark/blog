移动端项目优化的时候，需要这个插件来可视化查看打包大小，在此记录下

[插件官网地址](https://github.com/webpack-contrib/webpack-bundle-analyzer)


一.安装
```
    npm install --save-dev webpack-bundle-analyzer
```

二.使用方式：
1.作为插件使用（推荐）

在webpack配置文件，如果有生产配置文件放入生产配置文件里
require方式引入进来，然后在plugins配置项new一个实例即可
![image.png](https://upload-images.jianshu.io/upload_images/9645550-5ece64c3f61d7159.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
const webpack = require('webpack');
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = merge(common, {
    // devtool:'source-map',//开启将会生成map文件
    mode: 'production',
    plugins: [
        new webpack.DefinePlugin({
            'process.env.NODE_ENV': JSON.stringify('production')
        }),
        new BundleAnalyzerPlugin(),
    ],

    //代码分离 防止多入口重复打包bundle
    optimization: {
        splitChunks: {
            minChunks: 2,  //模块至少使用次数
            cacheGroups: { 
                vendor: { 
                    name: 'vendor',
                    test: /[\\/]node_modules[\\/]/,
                    chunks: 'all', 
                    priority: 2,  //2>0  nodulesmodules里的模块将优先打包进vendor
                },
                commons: {
                    name: "commons",   //async异步代码分割 initial同步代码分割 all同步异步分割都开启
                    chunks: "all", 
                    minSize: 30000,         //字节 引入的文件大于30kb才进行分割    
                    priority: 0,   //优先级，先打包到哪个组里面，值越大，优先级越高
                }               
            }
        }
    }
});
```
然后执行npm run build 既可

2.最为一个命令使用


You can generate it using `BundleAnalyzerPlugin` with `generateStatsFile` option set to `true` or with this simple command:（项目目录执行下面命令）

```source-shell
webpack --profile --json > stats.json
```

If you're on Windows and using PowerShell, you can generate the stats file with this command to [avoid BOM issues](https://github.com/webpack-contrib/webpack-bundle-analyzer/issues/47):（windwos10用户可以用powershell执行下面命令）

```
webpack --profile --json | Out-file 'stats.json' -Encoding OEM

```

Then you can run the CLI tool.
（再执行该命令即可）

```
webpack-bundle-analyzer bundle/output/path/stats.json
```

