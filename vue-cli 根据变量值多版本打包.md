导语:能遇到这个问题,说明你已经摸到前端中级的门槛了,加油.
需求举例:
前端项目,要打一个测试包,打一个预上线包,打一个线上包.这三个包对应的接口host是不同的,这时候需要打包的时候传一个变量,根据变量的值设置不同的host.
或者一个平台项目,分发给好多子公司使用,子公司需要私有化定制,比如不同的登录页,也需要打包的时候设置变量,然后进行判断.

解决方案:
第一步 
安装cross-env  一个支持以unix方式设置环境变量的包
```
npm i --save-dev cross-env
```
第二步
在package.json中设置scripts
```
"scripts": {
    "dev": "node build/dev-server.js",
    "start": "node build/dev-server.js",
    "build": "node build/build.js",
    "dev:test": "cross-env BRANCH_ENV=test node build/dev-server.js",
    "dev:prod": "cross-env BRANCH_ENV=prod node build/dev-server.js",
    "build:test": "cross-env BRANCH_ENV=test node build/build.js",
    "build:prod": "cross-env BRANCH_ENV=prod node build/build.js"
  },
```

第三步
在axios的配置文件中
```
instance.interceptors.request.use(
  function (config) {
    if (process.env == "test" || process.env.BRANCH == "test") {
             config.baseURL = ""    //改为test对应的host    
             return config;    
    }else{
             config.baseURL = ""    //改为prod对应的host 
             return config;
    }
  },
  error => {
    return Promise.reject(error);
  }
);
```

第四步
运行npm run build:prod  即可打出BRANCH值为prod的包
