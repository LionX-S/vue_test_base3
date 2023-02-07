<template>
	<!-- vue3组件中可以没有根标签 -->
	<h1>person组件</h1>
	<p>{{ name }}</p>
	<p>{{ age }}</p>
	<button @click="sayHello">click</button>
	<button @click="changeInfo">更改info</button>
</template>

<script>
	import { ref } from "vue";
	export default {
		name: "App",
		setup() {
			// 数据
      // ref实现响应式
			let name = ref("jim");
			let age = ref(18);

			// function
			function sayHello() {
				alert("name" + name.value + age.value + "岁");
			}

			function changeInfo() {
				name.value = "tim";
				age.value = 35;
			}
			return {
				name,
				age,
				sayHello,
				changeInfo
			};
		},
		components: {}
	};
</script>

<style></style>
