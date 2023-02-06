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
  
  $\quad$2.setup不能是一个async函数，因为返回值不再是return的对象，而是promise，模版看不到return对象中的属性。
