<template>
  <!-- vue3组件中可以没有根标签 -->
  <h1>person组件</h1>
  <p>{{ name }}</p>
  <p>{{ age }}</p>
  <button @click="sayHello">click</button>
</template>

<script>

export default {
  name: 'App',
  setup() {
    // 数据
    let name = 'jim';
    let age = 18
    
    // function
    function sayHello() {
      alert('name'+name+age+'岁')
    }
    return {
      name,
      age,
      sayHello
    }
  },
  components: {
    
  }
}
</script>

<style>

</style>
