<!--
 * @Author: xiehongbin
 * @Date: 2020-12-02 20:44:55
 * @LastEditTime: 2020-12-08 19:50:36
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \Programming-Notes\FrontEnd\Vue\Vue-Basis\Vue专题.md
-->
# Vue 专题笔记
## computed 和 watch
### computed 用法
要点： computed类型，通过Vue实例直接访问计算属性，箭头函数问题，数据缓存问题
1. computed是一个对象
- 类型一： 对象键名是字符串，键值是函数
- 类型二： 对象键名是字符串，键值是一个对象，包含set/get属性。当通过vm.computedName访问时，调用的是get方法，当通过vm.computedName = 1时，访问的是set方法。
2. 计算属性将被混入到Vue实例中，所有的getter和setter的this上下文都自动绑定为Vue实例。
3. 不要使用箭头函数来作为计算属性的键值，否则this将不会指向当前实例，但是当前实例也会作为第一个参数传递给箭头函数，要用时只需要接收即可。
4. 计算属性的结果会被缓存，当依赖的响应式property发生改变时，才会触发重新计算。

### watch用法
要点: watch对象类型问题，回调函数问题，$watch问题
1. watch属性也是一个对象：键名是一个表达式的字符串，键值可以是表示方法的字符串，也可以是函数（接收两个参数：newValue/oldValue），对象（用于配置高级属性：handler，deep，immediate），也可以数组
键值类型一：methods里面的方法名
键值类型二：函数类型，接收2个参数：newValue，oldValue
键值类型三：配置对象的形式 => 该配置对象的属性可以有 handler（回调函数），deep（是否对深层次属性进行监听），immediate（添加监听器之后，是否立即执行回调函数）
键值类型四：数组=> 可以为同一个表达式增加多个回调函数，数组元素的类型可以是上述类型中的任一。
2. 不应该使用箭头函数来作为watch的回调函数，会存在this指向错误问题。
3. `$watch` 可以通过vm.$watch实现监听的功能。
- 接收3个参数：
参数一： 表达式或者函数
参数二： 回调函数（或者对象）
参数三：配置对象 
  - deep属性 => 当监听数组时不需要设置为true 
  - immediate属性 => 不能在immediate属性设置为true时，取消当前属性的监听
```js
vm.$watch(function(){
  return this.a + this.b // 表示只要表达式`this.a + this.b`的值发生改变
  // 对应的回调函数就会被调用
  // 就像监听一个未被定义的计算属性
},callback)
```
- `vm.$watch` 会返回一个取消观察函数，用来停止触发回调函数
`watch`和`$watch`的区别: `$watch`需要手动移除监听，`watch`属性不需要

### comptuted和watch区别
watch需要数据变化时执行异步或者开销较大时使用，当一个属性影响多个属性时
computed 当一个属性受多个属性影响时
## 渲染函数和JSX
虚拟节点和虚拟DOM
Vue通过建立一个虚拟DOM来追踪自己要如何改变真实DOM。
createElement函数返回的就是虚拟DOM节点，简称VNode
它所包含的信息会告诉Vue页面上需要渲染什么节点，包括子节点的描述信息
虚拟DOM是我们对由Vue组件建立起来的整个VNode树的称呼。
格式：
```js
new Vue({
  el:'#root',fo
  render: function(createElement){
    // 返回VNode
    return createElement('h1',this.bolgTitle);
  }
})
```
render属性是一个函数，函数接收createElement方法作为参数，返回值为VNode
1. createElement() 接收三个参数
参数一：必选
- 一个HTML元素标签名
- 组件配置对象
- 异步函数 resolve了上述任一的async函数
  参数二:可选
- 对象类型，一个与模板中attribute对应的对象，可选
  参数三：字符串或者数组
- 子级虚拟节点，由createElement()返回
- 或者使用字符串生成文本节点
```js
// 渲染函数基本使用
new Vue({
  el:'#root',
  render:function(createElement) {
    return createElement('div',{
      // 参数二：属性对象
    },[
      '文本内容',
      createElement(MyComponent, {
        props: {
          someProps:'fooBar'
        }
      })
    ])
  }
})
```
参数二：属性对象
```js
{
  // 类型一:class style
  // 接收一个字符串、对象或者字符串和对象组成的数组：和v-bind:class 的语法一致
  class: {
    "foo": true,
    "bar":false
  },
  style: {
    color:'red',
    fontSize:'12px'
  },
  // 类型二 普通的HTML属性
  attrs:{
    id:'foo'
  },
  // 类型三 组件的prop  
  props:{
    myProps:'bar'
  },
  //类型四： DOM属性： 例如innerHTML 会覆盖v-html的内容
  dmoProps:{
    innerHTML:'baz'
  },
  // 类型五：事件监听
  on: {
    click: this.clickHandler
  },/
  // 类型六：nativeOn 仅用于组件,监听原生事件，而不是组件内部使用
  // vm.$emit触发的事件
  nativeOn: {
    click:this.nativeClickHandler // 注意区分 on属性
  },
  // 类型七： 自定义指令
  directives: [{
    name:'my-custom-directives',
    value:'2',
    expression:'1+1',
    arg:'foo',
    modifiers: {
      bar: true
    }
  }],
  // 类型八：插槽
  // 作用域插槽
  scopedSlots: {
    default:props => createElement('span',props.text);
  },
  // 如果组件是其他组件的子组件，需要为插槽指定名称
  slot:'name-of-slot',
  // 其他顶层属性
  key:'myKey',
  ref:'myRef',
  
}
```
```js
Vue.component("anchored-heading",{
  render:function(createElement){
    return createElement(
      'h'+this.level,
      [
        createElement('a', 
        {
          attrs: {
            name: headingId,
            href:'#'+headingId
          },
        },
        this.$slots.default)
      ]
    )
  }
  },
  props: {
    level: {
      type:Number,
      required: true
    }
  }
)
```
VNode 必须是唯一的
render: function (createElement) {
  var myParagraphVNode = createElement('p', 'hi')
  return createElement('div', [
    // 错误 - 重复的 VNode
    myParagraphVNode, myParagraphVNode
  ])i
}

用render实现v-for 或者 v-if
在渲染函数中用JavaScript的if/else 和 map 来重写
```js
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```
```js
props:['items']
render:function(createElement) {
  if(this.items.length) {
    return createElement('ul',this.items.map(function(item){
      return createElement('li',item.name)
    }))
  }else {
    return createElement('p','No items found');
  }
}
```
v-model
在渲染函数中需要自己实现响应的逻辑
```js
props:['value'],
render:function(createElement){
  var self = this;
  return createElement('input',{
    domProps: {
      value:self.value
    },
    on:{
      input:function(event){
        this.$emit('input',event.target.value);
      }
    }
  })
}
```
事件 按键修饰符


## 自定义指令
除了v-model、v-show等指令，Vue也允许注册自定义指令。
使用情况：需要对DOM元素进行底层操作
实现例子：自动获取焦点
```js
// 注册指令
// 使用Vue.directive来注册一个全局指令
Vue.directive('focus', {
  insert:function(el){
    // 聚焦元素
    el.focus();
  }
})
// 注册完之后可以在模板任何地方使用v-focus指令
<input v-focus />
```

### 注册指令
1. 全局注册
使用`Vue.directive`方法，接收两个参数，参数1为指令的名称，不需要加`v-`,参数2为指令的配置对象。
2. 局部注册
组件实例也接收一个directives的选项，类型为对象，每个自定义指令的名称为键名，指令配置对象为键值。

### 指令配置对象
全局注册组件时可以接受一个配置对象，这个对象中包含指令的一些钩子函数。
#### 钩子函数
1. bind： 只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
2. inserted： 被绑定元素插入到父节点时调用（仅仅保证父节点存在，但是不一定被插入文档中）
3. update： 所在组件的VNode更新时调用，但是可能发生在其子VNode更新之前。指令的值可能发生了改变，也可能没有。可以比较更新前后的值来忽略不必要的更新。（有疑问？）
4. componentUpdated 指令所在组件的VNode及其子VNode全部更新之后调用
5. unbind：只调用一次，指令与元素解绑时调用
#### 钩子函数参数
指令钩子函数会被嵌入以下参数：el、binding、vnode、oldVnode

1. el 指定所绑定的元素，可以用来直接操作DOM
2. binding 一个对象，包含以下property
- name 指令名
- value 指令的绑定值：`v-my-directive="1+1"`,value的值为2
- oldValue 指令绑定的前一个值，仅在update和componentUpdated钩子中可用。无论值是否改变都可用。
- expression 字符串形式的指令表达式：`v-my-directive="1+1"`,表达式为`"1+1"`
- arg 传给指令的参数，可选。`v-my-directive:foo`中，参数为`foo`
- modifiers: 一个包含修饰符的对象。例如：`v-my-directive.foo.bar`，修饰符对象为`{ foo: true, bar:true }`
3. vnode: 
4. oldVnode：。
注意：
除了el属性，其他参数都应该是只读的，切勿进行修改。如果需要在钩子间共享数据，建议通过元素的dataset来进行。
3. vnode：Vue编译生成的虚拟节点
4. oldVnode：上一个虚拟节点。仅在update和componentUpdate钩子中可用

#### 动态指令参数
指令的参数是可以动态,例如`v-mydirective:[argument]="value"`中，argument参数可以根据组件实例数据进行更新。
#### 函数简写
在很多时候，想在bind和update时触发相同行为，而不关系其他的钩子。
```
Vue.directive('color-swatch',function(el,binding){
  el.style.backgroundColor = binding.value;
})
```
即在注册指令的时候，参数2为一个方法，在这方法做对应的操作。
#### 对象字面量
如果指令需要多个值，可以传入一个JavaScript对象字面量。指令函数能够接收所有合法的JavaScript表达式。
```js
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```
即binding.value可以是一个值，也可以是一个对象


疑问:
自定义指令的实际使用例子
## 过滤器
Vue.js允许你使用自定义过滤器，可用于一些常见的文本格式化。
过滤器可以用在两个地方： 双花括号插值表达式和v-bind表达式
过滤器应该被添加到JavaScript表达式的尾部，由管道符号指示。
```js
// 使用
{{message | captialize }} 
<div v-bind:id="rawId | formatId"></div>
// 过滤器: 在一个组件的选项中定义本地的过滤器
filters: {
  capitalize: function(value){
    if(!value) return 
    value = value.toString;
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```
或者在创建Vue实例之前全局定义过滤器,通过Vue.filter
```js
Vue.filter('captalize',function(value) {
  if(!value) return 
    value = value.toString;
    return value.charAt(0).toUpperCase() + value.slice(1)
})
```
当全局过滤器和局部过滤器重名时，会采用局部过滤器。
过滤器可以串联，如`{{ message | filterA | fliterB }}`
表达式message的值将作为参数传入到函数中，然后将filterA的结果传递到filterB中。