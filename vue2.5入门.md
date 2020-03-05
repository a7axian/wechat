# vue2.5入门
## 1-1课程介绍
* 基础知识
* 案例实践
* TodoList
* Vue-cli(TodoList)
## 2-1创建第一个Vue实例
```
<div id="root">{{msg}}</div>	
<script>
	new Vue({
		el:"#root",
		data:{
			msg:"Hello World"
		}
	})
</script>
```
## 2-2挂载点、模板与实例
```
<div id="root">{{msg}}</div>	
<script>
	new Vue({
		el:"#root",
		data:{
			msg:"Hello World"
		}
	})
</script>
```
div标签为vue实例的挂载点
vue实例只处理挂载点中的内容
模板:挂载点内部的内容，可以放在vue实例中的template编写
```
<div id="root"></div>
<script>
	new Vue({
		el:"#root",
		template:"<h1>hello {{msg}}</h1>",
		data:{
			msg:"Hello World"
		}
	})
</script>
```
## 2-3Vue实例中的数据、事件和方法
思想转变:面向数据编程，不是面向dom编程

* 数据
{{}}:插值表达式  
`<h1>{{msg}}</h1>`  
`<h1 v-text="msg"></h1>` #会进行标签转义  
`<h1 v-html="msg"></h1>` #不会转义

* 事件绑定 v-on
```
<div id="root">
	<h1 v-on:click="handleClick">{{msg}}</h1>
</div>
<script>
	new Vue({
		el:"#root",
		data:{
			msg:"Hello",
			number:123,
		},
		methods:{
			handleClick:function(){
				this.msg="World"
			}
		}
	})
</script>
```
简写:  
`<h1 v-on:click="handleClick">{{msg}}</h1>`  
`<h1 @click="handleClick">{{msg}}</h1>`

## 2-4Vue中的属性绑定和双向数据绑定
* 属性绑定 v-bind
```
<div id="root">
	<div v-bind:title="'可以进行字符串拼接'+title">{{msg}}</div>
</div>
<script>
	new Vue({
		el:"#root",
		data:{
			msg:"hello world",
			title:"this is hello world"
		}
	})
</script>
```
简写:  
`<div v-bind:title="title">{{msg}}</div>`  
`<div :title="title">{{msg}}</div>`

* 双向数据绑定 v-model  
`<input v-model="content"/>`  
`<div>{{content}}</div>`

## 2-5Vue中的计算属性和侦听器
```
<div id="root">
	姓:<input v-model="firstname"/>
	名:<input v-model="lastname"/>
	<div>{{fullname}}</div>
	<div>{{count}}</div>
</div>
<script>
	new Vue({
		el:"#root",
		data:{
			firstname:'',
			lastname:'',
			count:0
		},
		computed:{
			fullname:function(){
				return this.firstname+' '+this.lastname
			}
		},
		watch:{
			firstname:function(){
				return this.count++
			},
			lastname:function(){
				return this.count++
			}
		}
	})
</script>
```

* 计算属性 computed
* 侦听器 watch
## 2-6一些指令
* v-if :值为true时标签存在,值为false时删除标签
* v-show :值为true时标签存在,值为false时使标签display:none
* v-for :循环显示数组内容
`<li v-for="(item,index) of list" :key='index'>{{item}}</li>`  
key值:提升效率

## 3-1todolist功能开发
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>TodoList</title>
		<script src="./vue.js"></script>
	</head>
	<body>
		<div id="root">
			<div>
				<input v-model="inputValue"/>
				<button @click="handleClick">提交</button>
			</div>
			<ul>
				<li v-for="(item,index) of list" :key="index">{{item}}</li>
			</ul>
		</div>
		<script>
			new Vue({
				el:"#root",
				data:{
					inputValue:'hello',
					list:[]
				},
				methods:{
					handleClick:function(){
						this.list.push(this.inputValue)
						this.inputValue=''
					}
				}
			})
		</script>
	</body>
</html>
```
## 3-2todolist组件拆分
组件:页面上的某一部分

* 全局组件  
`Vue.component('todo-item',{template:'<li>item</li>'})`

* 局部组件  
`var TodoItem={template:'<li>item</li>'}`   
需要在vue实例中声明   
`new Vue({el:"#root",components:{'todo-item':TodoItem}})`
## 3-3组件与实例的关系
一个组件就是一个vue实例。  
实例中如果没有模板属性template,实例会将挂载点中的所有标签当做模板。
## 3-3实现todolist的删除功能
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>TodoList</title>
		<script src="./vue.js"></script>
	</head>
	<body>
		<div id="root">
			<div>
				<input v-model="inputValue"/>
				<button @click="handleClick">提交</button>
			</div>
			<ul>
				<todo-item v-for="(item,index) of list":key="index":content="item":index="index"@delete="handleDelete"></todo-item>
			</ul>
		</div>
		<script>
			// 全局组件
			Vue.component('todo-item',{
				props:['content','index'],
				template:'<li @click="handleClick">{{content}}</li>',
				methods:{
					handleClick:function(){
						this.$emit('delete',this.index)
					}
				}
				})
					
			new Vue({
				el:"#root",
				data:{
					inputValue:'hello',
					list:[]
				},
				methods:{
					handleClick:function(){
						this.list.push(this.inputValue)
						this.inputValue=''
					},
					handleDelete:function(index){
						this.list.splice(index,1)
					}
				}
			})
		</script>
	</body>
</html>
```

* 父子组件的交互(难点)  
父组件向子组件传递参数:通过属性的形式  
子组件向父组件传递数据:通过发布定义模式`this.$emit('delete',this.index)`  