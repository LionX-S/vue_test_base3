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

