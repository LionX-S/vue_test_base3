# 一、常用的Composition API

## 1.拉开序幕的setup

$\quad$ 1.理解：Vue3.0中有一个新的配置项，值为一个函数

$\quad$ 2.setup是所有Composition API(组合API)*<mark>“表演的舞台”</mark>*

$\quad$ 3.组件中所用的数据方法等等，均要配置在setup中。

$\quad$ 4.setup函数的两种返回值：

$\quad\quad$ 1.若返回一个对象，则对象中的属性、方法，在模版中均可以直接使用。（重点关注）

$\quad\quad$ 2.若返回一个渲染函数：则可以自定义渲染内容。（了解）

$\quad$ 5.注意点：

$\quad\quad$ 1.尽量不要与Vue2.x配置混用

- Vue2.x配置（data,methods,computed）中可以访问到setup中的属性与方法。

- 但在setup中不能访问到vue2.x配置（data，methods，computed）

- 如果有重名，setup中的数据优先

$\quad\quad$ 2.setup不能是一个async函数，因为返回值不再是return的对象，而是promise，模版看不到return对象中的属性。

## 2.ref函数

- 作用：定义一个响应式数据

- 语法：```const xxx = ref(initValue)```
  
  - 创建一个包含响应式数据的引用对象（reference对象）
  
  - JS中操作数据：```xxx.value```
  
  - 模版中读取数据，不用.value,直接```<div>{{xxx}}</div>```

- 备注
  
  - 接收的数据可以是：基本类型、也可以是对象类型。
  
  - 基本类型的数据：响应式依然是靠```Object.defineProperty()   ```的get与set完成的
  
  - 对象数据类型：内部‘求助’了Vue3.0中的一个函数——```reactive```函数。

## 3.reactive函数

- 作用：定义一个对象类型的响应式数据

- 语法：```const 代理对象 = reactive(源对象)```接收一个对象（或数组），返回一个代理对象（proxy对象）

- reactive定义的响应式数据是“深层次的”

- 内部基于ES6的Proxy实现。通过代理对象操作源对象内部数据进行操作

## 4.Vue3.0中的响应式原理

### vue2.x的响应式

- 实现原理：
  
  - 对象类型：通过Object.defineProperty()对属性的读取、修改和拦截（数据劫持）
  
  - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。
    
    ```javascript
    Object.defineProperty(data, 'count', {
        get(){},
        set(){}
    })
    ```

- 存在问题：
  
  - 新增属性、删除属性，页面不会更新。
  
  - 直接通过下标修改数组，页面不会更新。

### Vue3.0的响应式

- 实现原理：
  
  - 通过Proxy（代理）：拦截对象中任意属性变化，包括：属性值的读写，属性的添加、属性的删除等。
  
  - 通过Reflect（反射）：对被代理对象的属性进行操作。
  
  - MDN文档中描述的Proxy与Reflect
    
    - Proxy：
    
    - Reflect：
      
      ```javascript
      new Proxy(data,{
          //拦截读取属性值
          get(target, prop){
              return Reflect.get(target,prop)
          }
          //拦截设置属性值或添加新属性
          set(target, prop, value) {
              return Reflect.set(target, prop, value)
          }
          //拦截删除属性
          deleteProperty(target, prop) {
              return Reflect.deleteProperty(target, prop)
          }
      })
      proxy.name='tom'
      ```

## 5.reactive对比ref

- 从定义数据角度对比：
  
  - ref用来定义：基本类型数据
  
  - reactive用来定义：对象（或数组）类型数据。
  
  - 备注：ref也可以用来定义对象（或数组）类型数据，它内部会自动通过**reactive**转为代理

- 从原理角度对比：
  
  - ref通过Object.defineProperty()的get与set来实现响应式（数据劫持）。
  
  - reactive通过使用Proxy来实现响应式（数据劫持），并通过Reflect操作源对象内部数据。

- 从使用角度对比：
  
  - ref定义的数据：操作数据需要```.value```,读取数据时模版中直接读取不需要```.value```。
  
  - reactive定义的数据：操作数据与读取数据均不需要```.value```。

## 6.setup的两个注意点

- setup的执行时机
  
  - 在beforeCreate之前执行一次，this是undefined

- setup的参数
  
  - props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性。
  
  - context：上下文对象
    
    - attrs：值为对象，包含组件外部传递过来，但没有在props配置中声明的属性，相当于```this.$attrs```
    
    - slots:收到的插槽内容，相当于`this.$slots`
    
    - emit:分发自定义事件的函数，相当于`this.$emit`

## 7.计算属性与监视

### 1.computed函数

- 与Vue2.x中computed配置功能一致

- 写法
  
  ```javascript
  import { reactive, computed } from "vue";
      export default {
          setup(props, context) {
              let person = reactive({
                  firstName: "",
                  lastName: "",
              });
              function hello() {
                  console.log("props====", props);
                  context.emit("hello", "666");
              }
        // 计算属性——简写
        let fullName = computed(() => {
          return person.firstName + person.lastName;
        })
        // 计算属性——完整写法
        // let fullName = computed({
        //   get() {
        //     return person.firstName + person.lastName;
        //   },
        //   set(value){
  
        //   }
        // })
  ```

### 2.watch函数

- 与Vue2.x中的watch配置功能一致

- 两个小‘坑’

  **待验证**（不确定最新版vue3已经解决？）
  
  - 监视reactive定义的响应式数据时，oldValue无法正常获取、强制开启了深度监视（deep配置失效）
  
  - 监视reactive定义的响应式数据中某个属性时：deep配置有效。
  
  ```javascript
  //情况一：监视ref定义的响应式数据
  watch(sum, (newValue,oldValue) => {
    console.log("sum变化",newValue,oldValue)
  },{immediate:true})
  //情况二：监视多个ref定义的响应式数据, 其中newValue与oldValue输出后也是数组
  watch([sum,msg], (newValue,oldValue) => {
    console.log("sum变化",newValue,oldValue)
  },{immediate:true})
  /*情况三：监视reactive所定义的一个响应式数据
      1.注意：此处无法正确获取oldValue
      2.注意：强制开启了深度监视（deep配置无效）
  */
  watch(person,(newValue,oldValue) => {
    console.log('person变化了',newValue,oldValue)
  },{deep:false})
  //情况四：监视reactive所定义的一个响应式数据中的某个属性
  watch(() => person.age,(newValue,oldValue) => {
    console.log('person变化了',newValue,oldValue)
  },{deep:false})
  //情况五：监视reactive所定义的一些响应式数据中的某个属性
  watch([() => person.age, () => person.name],(newValue,oldValue) => {
    console.log('person变化了',newValue,oldValue)
  },{deep:false})
  //特殊情况,监视reactive中某个嵌套的对象时，如果这个对象中的属性值变化，不会捕捉到，需要开启deep才能捕捉到
  watch(() => person.job,(newValue,oldValue) => {
    console.log('person变化了',newValue,oldValue)
  },{deep:true})
  ```
### 3.watchEffect函数
- watch的套路是：既要指明监视属性，也要指明监视回调。
- watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，就监视哪个属性。
- watchEffect有点像computed：
  - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
  - 而watchEffect跟注重的是过程（回调函数的函数体），所以不用写返回值。
  ```javascript
  //watchEffect所指定的回调中用到的数据只要发生变化，则重新执行回调
  watchEffect(() => {
    const x1 = sum.value
    const x3 = person.age
    console.log('watchEffect配置的回调执行了')
  })
  
  ```
## 8.vue3生命周期
 - vue2生命周期图
  ![vue2声明周期](https://v2.cn.vuejs.org/images/lifecycle.png)
 - vue3生命周期图
  ![vue3声明周期](https://cn.vuejs.org/assets/lifecycle.16e4c08e.png)

## 9.自定义hook函数
 - 什么是hook？本质是一个函数，把setup函数中使用的Composition api 进行了封装。
 - 类似于vue2.x中的mixin。
 - 自定义hook的优势：复用代码，让setup中的逻辑更清楚易懂。

## 10.toRef
 - 作用：创建一个ref对象，其value值指向另一个对象中的某个属性。
 - 语法：```const name = toRef(person, 'name')```
 - 应用：要将响应式对象中的某个属性单独提供给外部使用时。
 - 扩展：```toRefs```与```toRef```功能一致，但可以批量创建多个ref对象，语法```toRefs(person)```

# 三、其他Composition API
## 1.shallowReactive和shallowRef
- shallowReactive:只处理对象外层属性的响应式（浅响应式）
- shallowRef:只处理基本数据类型的响应式，不进行对象的响应式处理。
- 什么时候用？
  - 如果有一个对象数据，结构比较深，但变化时只是外层属性变化===>shallowReactive
  - 如果有一个对象数据，后续功能不会修改该对象中的属性，而是生成新的对象来替换===>shallowRef,这个方法只会处理基本数据类型（实现响应式），不会处理引用类型，而ref则会处理引用类型

## 2.realOnly与shallowReadonly
- readonly:让一个响应式数据变为只读的（深只读）
- shallowReadonly:让一个响应式数据变为只读的（浅只读）
- 应用场景：不希望数据被修改时
  ```javascript
  let name = ref('jim')
  name = readOnly(name)
  ```
## 3.toRaw与markRaw
 - toRaw:
   - 作用：将一个由reactive生成的响应式对象转为普通对象。
   - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新
 - markRaw：
   - 作用：标记一个对象，使其永远不会再成为响应式对象。
   - 应用场景：
  1.有些值不应被设置为响应式的，例如复杂的第三方类库等。
  2.当渲染具有不可变数据源的大列表时，跳过响应式转换可提高性能。
## 4.customRef
 - 作用：创建一个自定义的ref，并对其依赖项跟踪和更新触发进行显示控制。
 - 实现防抖效果
  ```javascript
  // 自定义ref 实现数据的防抖
	function myRef(value) {
		let timer;
		return customRef((track,trigger) => {
			return {
				get() {
					// 告诉vue这个value需要被跟踪
					track();
					return value;
				},
				set(newValue) {
					clearTimeout(timer);
					timer = setTimeout(() => {
						value = newValue
						trigger()//告诉vue去更新界面
					}, 500);
				}
			};
		});
	}
	let inputValue = myRef("123");
  ```
## 5.provide与inject
- 作用：实现祖孙组件间通信
- 套路：父组件有一个provide选项来提供数据，子组件有一个inject选项开始使用这些数据
- 具体写法：
  1.祖组件中：
  ```javascript
  setup() {
    ....
    let car = reactive({name:'奔驰',price:'40w'});
    provide('car',car)
  }
  ```
  2.后代组件中
  ```javascript
  setup() {
    ....
    let car = inject('car');
    return {car}
  }
  ```
## 6.响应式数据的判断
- isRef检查一个值是否为一个ref对象
- isReactive：检查一个值是否由reactive创建的响应式代理
- isReadonly：检查一个对象是否由readonly创建的只读代理
- isProxy：检查一个对象是否由reactive或者readonly方法创建的代理

# 四、Composition API的优势
## 1.传统Options API存在的问题
使用传统Option API中，新增或修改一个需求，就需要在data、methods、computed里修改
## 2.Composition API的优势
我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起。
# 五、新的组件
## 1.Fragment
- 在vue2中，组件必须有一个跟标签
- 在vue3中，组件可以没有跟标签，内部会将多个标签包含在一个Fragment虚拟元素中
- 好处：减少标签层级，减少内存占用
## 2.Teleport
- 什么是Teleport?Teleport是一种能够将我们的组件html结构移动到指定位置的技术。
  ```html
  <teleport to="移动位置（这里写选择器）">
    <div v-if="isShow" class="mask">
      <div>内容</div>
    </div>
  </teleport>
  ```
## 3.Suspense
- 等待异步组件渲染一些额外内容，让应用有更好的用户体验
- 使用步骤
  - 引入异步组件
  ```javascript
  import {defineAsyncComponent} from 'vue'
  const child = defineAsyncComponent(() => import('./component/xxx'))
  ```
  - 使用Suspense包裹组件，并配置好default与fallback
  ```html
  <template>
    <div class="app">
      <h3>
        我是APP组件
      </h3>
      <Suspense>
        <template v-slot:default>
          <child/>
        </template>
        <template v-slot:fallback>
          <h2>loading...</h2>
        </template>
      </Suspense>
    </div>
  </template>
  ```
# 六、Vue3其他更改
## 1.全局API的转移
- Vue2.x有许多全局API和配置。
  - 例如：注册全局组件、注册全局指令等。
  ```javascript
  //组册全局组件
  Vue.component('MyButton',{
    data: ()=> ({
      count: 0
    }),
    template: '<button @click="count++">{{count}}</button>'
  })
  //注册全局指令
  Vue.directive('focus',{
    inserted: el => el.focus()
  })
  ```
- Vue3.0中对这些api做出了调整：
  - 将全局的API，即Vue.xxx调整到应用实例（app）上
  
  |  2.x全局API（Vue）   | 3.x实例API（app）|
  |  ----  | ----  |
  | Vue.config.xxx  | app.config.xxx |
  | Vue.config.productionTip  | 移除了 |
  | Vue.component | app.component|
  | Vue.directive | app.directive |
  | Vue.mixin | app.mixin |
  | Vue.use | app.use |
  | Vie.prototype | app.config.globalProperties |

## 2.其他改变
- data选项应始终被生命为一个函数
- 过渡类名的更改
  - Vue2.x写法
  ```css
  .v-enter,
  .v-leave-to {
    opacity: 0;
  }
  .v-leave,
  .v-enter-to {
    opacity: 0;
  }
  ```
  -Vue3.x写法
  ```css
  .v-enter-from,
  .v-leave-to {
    opacity: 0;
  }
  .v-leave-from,
  .v-enter-to {
    opacity: 0;
  }
  ```
- 移除keyCode作为v-on的修饰符，同时也不再支持config.keyCodes
- 移除v-on.native修饰符
  - 父组件中绑定事件
  ```html
  <my-component
    v-on:close="handleComponentEvent"
    v-on:click="handleNativeClickEvent"
  />
  ```
  - 子组件中声明定义事件
  ```javascript
    <script>
      export default {
        emits:['close']
      }
    </script>
  ```
  - 移除过滤器
