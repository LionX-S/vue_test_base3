<template>
	<!-- vue3组件中可以没有根标签 -->
	<h1>person组件</h1>
	<p>{{ personInfo.name }}</p>
	<p>{{ personInfo.age }}</p>
	<p>{{ personInfo.hobby }}</p>
	<p>{{ personInfo.work.jobName }}</p>
	<p>{{ personInfo.work.salary }}</p>
	<button @click="sayHello">click</button>
	<button @click="changeInfo">更改info</button>
</template>

<script>
	import { ref, reactive } from "vue";
	export default {
		name: "App",
		setup() {
			// 数据
			// ref实现响应式
			// let name = ref("jim");
			// let age = ref(18);
			// reactive实现响应式
			let personInfo = reactive({
				name: "Fredy",
				age: 30,
				hobby: ["code", "guitar"],
				work: {
					jobName: "前端工程师",
					salary: "20K"
				}
			});
			// function
			function sayHello() {
				alert("name" + personInfo.name + personInfo.age + "岁");
			}

			function changeInfo() {
				// name.value = "tim";
				// age.value = 35;
				personInfo.name = "tim";
				personInfo.age = 35;
				personInfo.hobby[1] = "run";
				personInfo.work.salary = "30K";
			}
			return {
				// name,
				// age,
				personInfo,
				sayHello,
				changeInfo
			};
		},
		components: {}
	};
</script>

<style></style>
