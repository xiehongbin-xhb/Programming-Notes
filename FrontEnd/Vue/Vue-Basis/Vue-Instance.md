

# 怎么理解Vue
`Vue`是一套用于构建用户界面的渐进式框架。
**"渐进式"**
Vue核心的功能，是一个视图模板引擎，可以通过添加组件系统，客户端路由，大规模状态管理来构建一个完整的框架。
这些功能相互独立，可以在核心功能的基础上任意选用其他的部件。
所谓的渐进式，也就是Vue的使用方式。
# 如何构建用户界面
几乎所有类型的应用界面都可以抽象成一个组件树。
一个`Vue`应用是由一个通过`new Vue()`函数创建的根`Vue`实例，以及可选的嵌套的，可复用的组件树组成。
# Vue实例
## 创建Vue实例
1. 直接指定`el`属性
```
  var vm = new Vue({
    'el':"#root",
    'template':"<div>hello world</div>"
  });
```
2. 通过`$mount`方法进行挂载
```
  var vm = new Vue({
    'template':"<div>hello world</div>"
  })
  vm.$mount('#root');
```
## 创建Vue实例的配置对象
当一个`Vue`实例被创建时，可以传入一个配置对象，这个配置对象分为几类，数据，`DOM`，生命周期钩子，资源，其他等。
### 数据
#### data => Vue实例的数据对象
1. 根实例/组件data属性区别，箭头函数问题。
- 当一个组件被定义，data必须声明为一个返回数据对象的函数（一个函数，返回值是一个对象）。因为组件可能用来创建多个实例，如果data仍然为一个纯粹的对象，则所有实例都将共享引用同一个数据对象。
- 通过data函数，每次创建一个新实例之后，能够调用data函数，从而返回初始数据的一个全新副本数据对象。
- 如果data的值是一个箭头函数，则this不会指向这个组件的实例，不过仍然可以将其实例作为函数的第一个参数来访问
```js
data: (vm) => { { a: vm.MyProp }}
```
2. data属性和$data的关系
-实例创建之后，可以使用vm.$data访问到原始数据对象
- Vue实例上也代理了data对象所有的property，因此访问vm.a 等于访问vm.$data.a
- 以_或者$开头的property的属性不会被Vue实例代理(即不能通过vm.a这样的方式来访问这个属性)，因为它们可能和Vue内置的property，API方法冲突，可以使用例如vm.$data._property来访问这个属性。
3. 特殊的点
- 对象必须是纯粹的对象，含有零个或者多个key/value对。data应该只是数据，不推荐观察拥有状态行为的对象。（怎么理解？）
#### `props`  => 接收来自父组件的数据
`props`可以是数组或者对象。
1. 当`props`为数组时，使用方式如下
```js
Vue.component('test', {
  props:['prop1','prop2']
}
```
2. 当`props`为对象时，用于配置高级选项，如类型检测，默认值，自定义验证等。
- type 可以是下列构造函数中的一种:`String`,`Number`,`Boolean`,`Array`,`Object`,`Date`,`Function`,`Symbol`,任何自定义构造函数或者上述内容组成的数组。会检查一个`prop`是否是给定的类型，否则抛出警告。
- `default ：any `为该`prop`指定一个默认值。如果该`prop`没有被传入，则换做用这个值。对象或者数组的默认值必须从一个工厂函数返回。
- `require：Boolean·`定义该`prop`是否是必填项，在非生产环境下，如果这个值为`truthy`，且该`prop`没有被传入，则控制台警告将会抛出。(非生产环境下？ 指的是开发环境下会有警告，生产环境？)
- `validator: Function` 自定义验证函数会该`prop`的值作为唯一的参数。在非生产环境下，如果该函数返回一个`falsy`的值，也就是验证失败。一个控制台警告将会被抛出。

注意点：
设置默认值时，对象或者数组的默认值必须从一个工厂函数返回。

#### methods => 实例上自定义方法
method将被混入到Vue实例中，可以通过实例直接访问这些方法，或者在指令表达式张使用，方法中的this自定义绑定为Vue实例。
不应该使用箭头函数来定义methods函数（箭头函数this丢失问题）。
#### computed　计算属性
- 理解：相当于在Vue实例上多挂载一个属性，只不过这个属性是data对象内的某个属性或者某几个属性通过计算得出来的
- 也就是计算属性将被混入到Vue实例中，所有getter和setter的this上下文自动地绑定为Vue实例
- 计算属性的结果会被缓存，除非依赖的响应式property变化才会重新计算。
- 用法：两种 =  函数或者对象(get属性/set属性)
```js
var vm = new Vue({
	data:{a:1},
	computed:{
		aDouble:function() {
			return this.a * 2
		},
		// 读取和设置
		aPlus: {
			get:function(){
				return this.a + 1
			},
			set: function(){
				this.a = v - 1
			}
		}
	}
})
```
#### `watch` => 监听器对象
- 理解：一个对象，键名是需要观察的表达式，值是对应的回调函数。值也可以是方法名，或者包含选项的对象。（键名：表达式；键值：函数，对象，数组）
- 添加监听的时机：`Vue`实例化时调用`$watch`，遍历`watch`对象的每个一个`property`。
```js
  var vm = new Vue({
    data: {
        a:1,
        b:2,
        c:3,
        d:4,
        e:{
            f:{
                g:5
            }
        }
    },
    watch: {
        a:function(newValue,oldValue){
            console.log('newValue',newValue);
            console.log('oldValue',oldValue);
        },
        // 方法名：
        b:'someMethod',
        c:{
            // 该回调函数会任何被侦听的对象的property改变时被调用，不论其被嵌套多深
            handler:function(newValue,oldValue){ }
            deep:true
        },
        d:{
        	handler:'someMethod',
            immediate:true //该回调将会在侦听开始之后被立即调用
    	},
        e:[
            // 可传入handler数组，当e的值发生改变时，会逐一调用
            'handle1',function handle2(newValue,oldValue){
                
            },
            {
                handler:function handler3(newValue,oldValue){}
            }
        ]
    }
})
```
- 注意：配置对象的`immediate`属性，`deep`属性代表的意义。
#### propsData
只用于new创建的实例中，创建实例时传递props，主要作用是方便测试。(有疑问？)
```js
var Comp = Vue.extend({
	props:['msg'],
	template:'<div>{{msg}}</div>'
})
// 组件继承
var vm = new Comp({
	propsData:{
		msg:'hello'
	}
});
```
### DOM
#### el 
- 字符串或者 Element类型
- 只在用new 创建实例时生效
- 作用：提供一个在页面上已经存在的DOM元素作为Vue实例的挂载目标，可以是CSS选择器，也可以是一个HTMLElement实例。
在挂载之后，才能通过vm.$el访问到。

#### template
- 字符串类型
- 一个字符串模板，模板将会替换挂载的元素，挂载元素的内容将会被忽略，除非模板的内容有分发插槽(这种情况会发生什么？)
- 如果Vue选项中有包含渲染函数render属性，该模板template属性将会被忽略。
#### render
类型： 函数 => `(createElement) => VNode `
详细： 字符串模板的代替方案。
- 该渲染函数接收一个createElement方法作为第一个参数用来创建VNode
#### renderError
1. 类型：
2. 详细： 
- 只在开发环境下工作
- 当render函数遭遇错误时，提供另外一种渲染输出，其错误将会作为第二个参数传递到renderError，这个功能配合hot-reload非常实用
```js
new Vue({
  render(h) {
    throw new Error('渲染错误了') // 模拟render过程中发生错误了
  },
  renderError(h,err) {
    return h('pre',{ style: {color: 'red' }},err.stack);
  }
})
```
### 生命周期钩子
所有的生命周期钩子自动绑定this为实例，这意味着不能使用箭头函数来定义一个生命周期方法。
注意： 用一段话来描述整个Vue的生命周期，以及整理一些重要的生命周期函数主要用处。
#### beforeCreate
#### created
#### beforeMount
#### mounted
#### beforeUpdate
#### updated
#### beforeDestroy
#### destroyed
#### acitvated
#### deactivated
#### errorCaptured
### 资源
#### components 组件
包含Vue实例可用组件的哈希表。参考文章Vue Component
#### directives 自定义指令
包含Vue实例可用指令的哈希表。自定义指令。 参考文章 Vue Directives
#### filters 过滤器
包含Vue实例可用过滤器的哈希表。参考文章 Vue Filters
### 组合
#### parent
parent属性用于 指定已创建实例的父实例，在两者之间建立父子关系。子实例可以this.$parent访问父实例，子实例被推入父实例的$children数组中。
注意：节制地使用$parent 和 $children，它们的主要目的是作为访问组件的应急方法。更推荐使用props和events实现父子组件通信。
#### mixins 
参考 Vue Mixins文章。
#### extends 
参考 Vue Extends 文章
#### project / inject
参考 Vue 组件通信 文章

### 其他
#### name
#### delimeiters
#### functionals
#### model
#### inheritAttrs
#### comments

## Vue实例属性
### `$data`

   `Vue`实例观察的数据对象，`Vue`实例代理了`data`对象`property`的访问。

### `$props`

   当前组件接收到的props对象。`Vue`实例代理了`props`对象`property`的访问.

### `$el`

   `Vue`实例使用的根DOM元素

### `$options`

   - 定义：用于当前Vue实例的初始化选项

   - **只读**

   - 需要在选项中包含自定义property时会有用处（访问自定义属性，其他常规属性可以通过vm直接访问）

     ```js
     new Vue({
         customOption:'foo',
         created:function(){
             console.log(this.options.customOption)// => 'foo'
         }
     })
     ```

### `$parent`

   父实例，只读

### `$root`

   当前组件的根`Vue`实例，如果当前实例没有父实例，此实例将会是其自己。

### `$children`

   - 由`Vue`实例构成的数组

   - 只读
   - 表示当前实例的直接子组件。需要注意的是`$children`并不会保证顺序，也不是响应式的。

### `$slots`

   - 只读
   - 非响应式
   - 用来访问被插槽分发的内容。每个具名插槽都有与其相应的property（例如 `v-slot:foo`中的内容将会在`vm.$slots.foo`中被找到）。`default` `属性包含所有没有被包含在具名插槽中的节点。
   - 使用渲染函数书写一个组件时，访问`vm.$slots`最有帮助。

### `$scopedSlots`

   - 只读
   - 用来访问作用域插槽
   - 对于包括默认插槽在内的每一个插槽，该对象都包含一个返回相应`VNode`的函数
   - 作用域插槽函数保证返回一个`VNode`数组，除非在返回值无效的时候返回`undefined`

### `$refs`

  - 只读
  - 一个对象，持有注册过ref属性的所有DOM元素和组件实例

### `$isServer`

  - 当前Vue实例是否运行于服务器

### `$attrs`

  - 只读

  - 包含了父作用域下不作为prop被识别且获取的attrbute绑定（除了class和style）
  - 当一个组件没有声明任何prop时，这里会包含所有父作用域的绑定，并且可以通过`v-bind= "attrs"`传入内部组件

### `$listeners`

  - 只读
  - 包含了父作用域下的（不含.native修饰器的）v-on时间监听器。它可以通过`v-on="$listeners"`传入内部组件


## Vue实例方法
### 数据
#### vm.$watch
1. 参数
- 参数1 字符串或者函数
- 参数2 函数或者对象，表示回调函数
- 参数3 选项：  immediate属性/deep属性
```js
// 监听函数
vm.$watch(
  function () {
    // 表达式 `this.a + this.b` 每次得出一个不同的结果时
    // 处理函数都会被调用。
    // 这就像监听一个未被定义的计算属性
    return this.a + this.b
  },
  // 参数2
  function (newVal, oldVal) {
    // 做点什么
  },
  // 参数3
  {
    deep:true,
    immediate:true
  }
)
```
2. vm.$watch返回一个取消观察函数，用来停止触发回调
#### vm.$set
1. 参数
- 对象或者数组
- 属性名或者索引
- 要设置的值
2. vm.$set是全局API Vue.set的别名

#### vm.$delete
1. 参数
- 对象或者数组
- 属性名或者索引
2. vm.$delete是Vue.delete的别名
### 事件
#### vm.$emit
1. 参数
- 参数1 事件名
- 参数2 触发事件时需要传递的参数
触发一个事件，可带参数，也可不带参数
#### vm.$on
1. 参数
- event事件名
- callback 回调函数
2. 用法：监听当前实例上的自定义事件，函数可以由vm.$emit触发。回调函数会接收所有传入事件触发函数的额外参数
#### vm.$once
1. 监听一个自定义事件，但是只触发一次，一旦触发之后，监听器就会被移除
#### vm.$off
1. 参数
- 事件名
- 回调函数
2. 如果不传参数，则会移除所有的事件监听器
2. 如果只提供了事件，则移除该事件所有的所有监听器
3. 如果同时提供了事件与回调，则移除这个事件的这个回调函数。
### 生命周期
#### vm.$mount
手动挂载Vue实例
#### vm.$forceUpdate

#### vm.$nextTick
#### vm.$destroy

## Vue指令
### v-text
### v-html
### v-show
### v-if
### v-else-if
### v-for
### v-on
### v-bind
### v-model
### v-slot
### v-pre
### v-cloak
### v-once


## Vue知识专题
### computed和watch，$watch
### 插槽，v-slot,v-slots，v-scopedSlots
### 自定义指令
### 过滤器
### 函数式组件
### Vue过渡动画
### 渲染函数
###　混入