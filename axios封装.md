main.js
```
import AxiosPlugin from './assets/js/axiosPlugin.js'
Vue.use(AxiosPlugin)
```
axiosPlugin.js
```
import axios from 'axios'
import Vue from 'vue'
import headers from './axios_headers'
import router from '../../router/index.js'

const Axios = axios.create({
  timeout: 15000,
})

Axios.interceptors.request.use(
    config => {
        return headers.initheaders(config)
    },
    error => {
        return Promise.reject(error);
    }
);

Axios.interceptors.response.use(
    response => {
      if(response.data.code != 0){
        window.$Vue.$message({
          message:response.data.message,
          center:true,
          type:'error'
        })
        return response;
      }
      return response
    },
    error => {
        // console.log('is error')
        // console.log(error)
onst { config, code, message } = error
        // console.log(message)
         if (code === 'ECONNABORTED' || message === 'Network Error') { // 请求超时
               window.$Vue.$message({
                 message:'网络异常,请检查网络连接',
                 center:true,
                 type:'error'
               })
               return Promise.reject(error)
         }

         window.$Vue.$message({
           message:'服务端出错 : '+error.message,
           center:true,
           type:'error'
         })
  
         return Promise.reject(error)
    }
)


export default {
  install(Vue) {
    Object.defineProperty(Vue.prototype, '$http', { value: Axios })
  }
}

```
axios_header.js
```
const initheaders = function (config){
	if(localStorage.accesstoken){
		config.headers.token = localStorage.accesstoken
	}

	if(config.method == 'post'){
		if(!config.data){
			config.data = {}
		}
		config.data.appkey = 'NOVO_EDU_BEAUTY_SCIENCE_PC'
		config.data.accessToken = localStorage.accesstoken
	}

	if(config.method == 'get' ){
		if(!config.params){
			config.params = {}
		}
		config.params.appKey = 'NOVO_EDU_BEAUTY_SCIENCE_PC'
	}
    

	if(process.env == "production" || process.env.NODE_ENV == "production"){
	    config.baseURL  = "https://" + window.$Vue.$clientSDK.getHost() + '/portal'
	    //config.baseURL  = "https://dev.bos.kquestions.com/portal"
	}
	return config;
}

export default {
	initheaders:initheaders
}
```
