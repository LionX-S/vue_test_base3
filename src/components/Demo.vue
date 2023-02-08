<template lang="">
	<h1>Demo组件</h1>
	姓：<input
		type="text"
		v-model="person.firstName" />
	名：<input
		type="text"
		v-model="person.lastName" />
	<br />
	<span>姓名：{{ fullName }}</span>
	<br />
	<span>年龄：{{ age }}</span>
	<button @click="age++">age+1</button>
	<span>身高：{{ height }}</span>
	<button @click="height++">height+1</button>
	<br />
	<span>工作： {{ person.job.jobName }}</span>
	<button @click="changeJob">change job</button>
</template>
<script>
	import { ref, reactive, computed, watch } from "vue";
	export default {
		name: "Demo",
		// 有props和自定义事件时，要子组件接收，不然会报警告
		setup(props) {
			let age = ref(18);
			let height = ref(180);
			let person = reactive({
				firstName: "",
				lastName: "",
				job: {
					jobName: "driver"
				}
			});

			let fullName = computed(() => {
				return person.firstName + person.lastName;
			});

			// watch可以监听到ref定义的数据
			watch(age, (newValue, oldValue) => {
				console.log("age变化了", newValue, oldValue);
			});
			// 多个ref定义的也可以监听到
			watch([age, height], (newValue, oldValue) => {
				console.log("多个变化", newValue, oldValue);
			});

			// 监听reactive定义的值 无法争取获取oldValue
			watch(person, (newValue, oldValue) => {
				console.log("person changed", newValue, oldValue);
			});
			// 监听reactive定义的对象中的某个属性,可以拿到这个属性的oldValue
			watch(
				() => person.firstName,
				(newValue, oldValue) => {
					console.log("person first name changed", newValue, oldValue);
				}
			);
			// 监听reactive定义的对象中的多个属性,可以拿到这个属性的oldValue
			watch(
				[() => person.firstName, () => person.lastName],
				(newValue, oldValue) => {
					console.log(
						"person first name and last name changed",
						newValue,
						oldValue
					);
				}
			);

			//特殊情况,监视reactive中某个嵌套的对象时，如果这个对象中的属性值变化，不会捕捉到，需要开启deep才能捕捉到
			watch(
				() => person.job,
				(newValue, oldValue) => {
					console.log("person中job对象变化了", newValue, oldValue);
				},
				{ deep: true }
			);

			function changeJob() {
				person.job.jobName = "farmer";
			}
			return {
				age,
				height,
				person,
				fullName,
				changeJob
			};
		}
	};
</script>
<style lang=""></style>
