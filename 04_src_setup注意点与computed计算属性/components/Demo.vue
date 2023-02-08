<template lang="">
	<h1>Demo组件</h1>
	<button @click="hello">测试父组件给绑定的自定义事件</button>
	<br />
	姓：<input
		type="text"
		v-model="person.firstName" />
	名：<input
		type="text"
		v-model="person.lastName" />
	<br />
	<span>姓名：{{fullName}}</span>
</template>
<script>
	import { reactive, computed } from "vue";
	export default {
		name: "Demo",
		// 有props和自定义事件时，要子组件接收，不然会报警告
		props: ["testProps"],
		emits: ["hello"],
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
			return {
				person,
        fullName,
				hello
			};
		}
	};
</script>
<style lang=""></style>
