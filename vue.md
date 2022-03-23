### 一、vue响应式

1.vue2.x响应式原理

通过底层的js的Object.defineProperty的get和set方法来监听创建的vue实例中vm中data的变化，通过vm对象来代理_data对象中属性，为每一个添加到vm上的属性都指定一个getter/setter，从而读写data中的属性

- 对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）。

- 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。

问题：

1.新增数据、删除属性界面不会更新。（因为新增的数据和属性没有getter和setter）

2.直接通过下标修改数组界面不会自动更新

Object.defineProperty的一些问题:

1. 递归遍历所有的对象的属性，这样如果我们数据层级比较深的话，是一件很耗费性能的事情
2. 只能应用在对象上，不能用于数组
3. 只能够监听定义时的属性，不能监听新加的属性，这也就是为什么在vue中要使用Vue.set的原因，删除也是同理



<img src="C:\Users\Script\Documents\Scrshot\20220221222738.png" alt="20220221222738" style="zoom:;" />

2.vue3.x响应式

ref-->RefImp对象

reactive-->Proxy对象

底层利用es6的window.Proxy来实现，也包含getter/setter，添加新属性触发setter，删除属性触发deleteProperty函数，并且利用反射对象返回Reflect.get/set/deleteProperty(xxx)。reactive函数就是实现对proxy的封装。

- 通过Proxy（代理）:  拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等。
- 通过Reflect（反射）:  对源对象的属性进行操作。

从原理角度对比：

-  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
-  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据

发布订阅者模式：

双向数据绑定的原理：通过Object.defineProperty给每个发布者或者说需要观察的对象Observer添加getter和setter方法，只要该对象数据发生变化那么就触发setter方法了就会通知Watcher订阅者，订阅者有多个由消息订阅器dep统一管理，Compile负责编译模板，将模板中的变量转换成数据渲染出来，同时将每个指令对应的节点绑定更新函数转化成订阅者一旦数据变化就会收到通知，更新视图。MVVM作为数据绑定的入口，整合了Observer Watcher Compile三者通过Observer监听自己model数据变化，Compile解析模板指令，利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化--视图更新，视图更新--数据变化的效果。






### 二、watch函数

1.watch

- 与Vue2.x中watch配置功能一致
- 两个小“坑”：

  - 监视reactive定义的响应式数据时：oldValue无法正确获取、强制开启了深度监视（deep配置失效）。（因为底层是proxy对象，直接引用的根数据）
  - 监视reactive定义的响应式数据中某个属性时：deep配置有效。
  - person对象是一个代理Proxy;但是Person内部的对象不是Proxy,使用Deep开启内部对象的Proxy;



```js
//情况一：监视ref定义的响应式数据
watch(sum,(newValue,oldValue)=>{
	console.log('sum变化了',newValue,oldValue)
},{immediate:true})

//情况二：监视多个ref定义的响应式数据
watch([sum,msg],(newValue,oldValue)=>{
	console.log('sum或msg变化了',newValue,oldValue)
}) 

/* 情况三：监视reactive定义的响应式数据
			若watch监视的是reactive定义的响应式数据，则无法正确获得oldValue！！
			若watch监视的是reactive定义的响应式数据，则强制开启了深度监视 
*/
watch(person,(newValue,oldValue)=>{
	console.log('person变化了',newValue,oldValue)
},{immediate:true,deep:false}) //此处的deep配置不再奏效

//情况四：监视reactive定义的响应式数据中的某个属性
watch(()=>person.job,(newValue,oldValue)=>{
	console.log('person的job变化了',newValue,oldValue)
},{immediate:true,deep:true}) 

//情况五：监视reactive定义的响应式数据中的某些属性
watch([()=>person.job,()=>person.name],(newValue,oldValue)=>{
	console.log('person的job变化了',newValue,oldValue)
},{immediate:true,deep:true})

//特殊情况
watch(()=>person.job,(newValue,oldValue)=>{
    console.log('person的job变化了',newValue,oldValue)
},{deep:true}) //此处由于监视的是reactive素定义的对象中的某个属性，所以deep配置有效
```

2.watchEffect

- watch的套路是：既要指明监视的属性，也要指明监视的回调。
- watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。
- `watchEffect` 的回调方法内有一个很重要的方法，用于清除副作用。它接受的回调函数也接受一个函数 `onInvalidate`。名字不重要，重要的是它将会在 `watchEffect` 监听的变量改变之前被调用一次。
- watchEffect有点像computed：

  - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
  - 而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值。

```js
//watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
watchEffect(()=>{
    const x1 = sum.value
    const x2 = person.age
    console.log('watchEffect配置的回调执行了')
})
```

### 三、生命周期

- Vue3.0中可以继续使用Vue2.x中的生命周期钩子，但有有两个被更名：
  - ```beforeDestroy```改名为 ```beforeUnmount```
  - ```destroyed```改名为 ```unmounted```
- Vue3.0也提供了 Composition API 形式的生命周期钩子，与Vue2.x中钩子对应关系如下：
- 组合式api的钩子比用配置项写快（带on的快）
  - `beforeCreate`===>`setup()`
  - `created`=======>`setup()`
  - `beforeMount` ===>`onBeforeMount`
  - `mounted`=======>`onMounted`
  - `beforeUpdate`===>`onBeforeUpdate`
  - `updated` =======>`onUpdated`
  - `beforeUnmount` ==>`onBeforeUnmount`
  - `unmounted` =====>`onUnmounted`



### 四、箭头函数与普通函数

在常规函数中，关键字 this 表示**调用该函数的对象**，可以是窗口、文档、按钮或其他任何东西。

对于箭头函数，this 关键字始终表示**定义箭头函数的对象**。	

