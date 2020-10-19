mixin:混入.
vue中是指定义一个对象,该对象选择性拥有deta,methods,mounted等vue组件的属性,
引用混入对象的vue组件会继承该对象.

混入在好多语言中都有这个概念,是对装饰器模式的一种实现方式.

如果有后端开发经验,mixin很好理解.

其实,在vue中引入一个工具库文件util.js实质也是混入.
混入解决的是不破坏原来组件结构,又能实现组件功能的需求
mixin在vue中专指混入类vue组件.
和直接引入util.js的区别是,框架的mixin可以直接调用组件的变量方法等属性,其实就是this作用域直接绑定到引用组件上
比如util.js有个格式化时间函数,需要传参进去,然后获取返回结果
而混入可以直接调用相应data值进行计算


比如下面代码 在main.js混入一个全局节流函数
```
Vue.mixin({
	methods:{
		debounce_submit(){
		   clearTimeout(window.timeout)
		   window.timeout = setTimeout(()=>{
		      this.onSubmit()
		   },1000)
		},
	}
})
```
test.vue
```
<div @click="debounce_submit"></div>
methods:{
onSubmit(){
  xxxx
}
}
```
在test.vue中可以直接调用,如果还有别的组件也可以直接调用,而且可以不用传参数进去,提高代码复用度

同理,全局变量也可以采用mixin的方式.

mixin更多是在维护项目上使用,不想对原代码改动太多,直接混入.
或者混入fliters以替代utils.js里的部分格式化功能

但是mixin在vue2中过渡使用会有一系列问题,比如一个组件有多个混入,不好确定组件内函数是混入进来的还是自己自带的,混入的话是混入的哪个mixin.
所以vue3推出hook 一大部分也是解决这个问题