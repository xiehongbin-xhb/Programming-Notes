# 怎么理解Vue
Vue是一套用于构建用户界面的渐进式框架。
***"渐进式"**
Vue核心的功能，是一个视图模板引擎，可以通过添加组件系统，客户端路由，大规模状态管理来构建一个完整的框架。
这些功能相互独立，可以在核心功能的基础上任意选用其他的部件。
所谓的渐进式，也就是Vue的使用方式。
# 如何构建用户界面
几乎所有类型的应用界面都可以抽象成一个组件树。
一个Vue应用是由一个通过new Vue()函数创建的根Vue实例，以及可选的嵌套的，可复用的组件树组成。
# Vue实例
## 创建Vue实例
1. 直接指定el属性
```
  var vm = new Vue({
    'el':"#root",
    'template':"<div>hello world</div>"
  });
```
2. 通过$mount方法进行挂载
```
  var vm = new Vue({
    'template':"<div>hello world</div>"
  })
  vm.$mount('#root');
```
## 创建Vue实例的配置对 象
当一个Vue实例被创建时，可以传入一个配置对象，这个配置对象分为几类，数据，DOM，生命周期钩子，资源，其他等。
重要：
1. data和props
2. computed和watch
3. render和renderError
4. 
### 数据
#### data Vue实例的数据对象
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
#### props 接收来自父组件的数据
props可以是数组或者对象。
1. 当props为数组时，使用方式如下
```js
Vue.component('test', {
  props:['prop1','prop2']
}
```
2. 当props为对象时，用于配置高级选项，如类型检测，默认值，自定义验证等。
- type 可以是下列构造函数中的一种:`String`,`Number`,`Boolean`,`Array`,`Object`,`Date`,`Function`,`Symbol`,任何自定义构造函数或者上述内容组成的数组。会检查一个`prop`是否是给定的类型，否则抛出警告。
- `default ：any `为该`prop`指定一个默认值。如果该`prop`没有被传入，则换做用这个值。对象或者数组的默认值必须从一个工厂函数返回。
- `require：Boolean·`定义该`prop`是否是必填项，在非生产环境下，如果这个值为`truthy`，且该`prop`没有被传入，则控制台警告将会抛出。(非生产环境下？ 指的是开发环境下会有警告，生产环境？)
- `validator: Function` 自定义验证函数会该`prop`的值作为唯一的参数。在非生产环境下，如果该函数返回一个`falsy`的值，也就是验证失败。一个控制台警告将会被抛出。

注意点：
设置默认值时，对象或者数组的默认值必须从一个工厂函数返回。

#### methods 实例上自定义方法
method将被混入到Vue实例中，可以通过实例直接访问这些方法，或者在指令表达式张使用，方法中的this自定义绑定为Vue实例。
不应该使用箭头函数来定义methods函数（箭头函数this丢失问题）。
#### computed
#### watch
#### propsData
### DOM
#### el
#### template
#### render
#### renderError
### 生命周期钩子
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
#### directives 自定义指令
#### filters 过滤器
### 其他
#### name
#### delimeiters
#### functionals
#### model
#### inheritAttrs
#### comments
## Vue实例属性
### 
## Vue实例方法
