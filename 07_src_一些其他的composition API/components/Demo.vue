<template lang="">
	<h1>Demo组件</h1>
	<span>姓名：{{ name }}</span>
	<br />
	<span>年龄：{{ age }}</span>
	<br />
	<span>薪资：{{ salary }}</span>
	<hr />
	<input
		type="text"
		v-model="inputValue" />
	<span>{{ inputValue }}</span>
</template>
<script>
	import { ref, reactive, toRef, customRef } from "vue";
	export default {
		name: "Demo",
		setup(props) {
			let person = reactive({
				name: "jim",
				age: 19,
				job: {
					salary: "20k"
				}
			});
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
			// return {
			// 	person
			// };

			// 使用toRef 模版中就可以直接使用name、age、salary，并且是响应式的
			return {
				name: toRef(person, "name"),
				age: toRef(person, "age"),
				salary: toRef(person.job, "salary"),
				inputValue
			};
		}
	};
</script>
<style lang=""></style>
