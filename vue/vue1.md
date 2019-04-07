### 前言
公司最近上的新项目的前端都是VUE做的，之前用Dubbo做接口和前端调试时，很好奇这块是怎样交互的。后来看了项目源码，原来是用SPringMVC Rest接口去泛化调用Dubbo接口获取数据，再通过自身的Router功能来完成交互的，看前端华仔写代码也是溜得飞起，几乎和后端一样，瞬间来了兴趣，也就来了解下前后端分离的项目如何来开发。

VUE对我而言虽然是一个新东西，但并不代表前端对自己是一个新事物。html，css，js这前端三剑客是学习这些前端框架的基础，恰恰也是自己会的。以下作为一个后端开发人员对VUE的学习历程。

#### 学习经历
1. VUE的官网文档是必须去参阅的，网址在这里：https://cn.vuejs.org/v2/guide/，不需要了解太详细，感受下MVVM框架的魅力和最基本的语法规则即可。
2. 文档看的太枯燥可以去找一套入门视频，个人推荐慕课网上的这套视频：https://www.imooc.com/learn/980，虽然不会讲的很详细，但作为VUE入门的东西来参阅是没任何问题的，我花了大概两天完成。
3. 之前的还是概念性的入门，离实际的项目开发还是很远，个人认为，看经典的代码demo和总结性的文字非常重要，我在github上找到了一个仓库：https://github.com/WYseven/vue2-basic-demo，里面有很多的经典demo，目前自己也在努力消化中。

#### 对VUE的认识 
1. 模板结构
```
<template>
 //to do   放html代码     -- 展示层
</template>
<script>
 //to do   放js代码       -- 逻辑层 
</script>
<style>
//to do    放css样式代码  -- 样式层
<style>
```
2. 七大属性
```
<script>
	var vm = new Vue({
		el:  '#app',  //节点属性 
		data: {       
		   //to do    //数据属性
		},
		methods: {
			//to do   //方法属性
		},
        watch: {
           //to do    //监听属性
        },
        computed: {
           //to do    //计算属性
        },
        template:{
           //to do    //模板属性 
        },
        render: {
           //to do    //渲染属性 
        }
	})
</script>  
```