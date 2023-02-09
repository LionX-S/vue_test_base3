<template lang="">
	<h1>Demo组件</h1>
	<span>{{ num }}</span>
	<button @click="num++">+1</button>
</template>
<script>
	import {
		ref,
		onBeforeMount,
		onMounted,
		onBeforeUpdate,
		onUpdated,
		onBeforeUnmount,
		onUnmounted
	} from "vue";
	export default {
		name: "Demo",
		setup(props) {
			console.log("setUp");
			let num = ref(0);

			onBeforeMount(() => {
				console.log("onBeforeMount");
			});
			onMounted(() => {
				console.log("onMounted");
			});
			onBeforeUpdate(() => {
				console.log("onBeforeUpdate");
			});
			onUpdated(() => {
				console.log("onUpdated");
			});
			onBeforeUnmount(() => {
				console.log("onBeforeUnmount");
			});
			onUnmounted(() => {
				console.log("onUnmounted");
			});

			return {
				num
			};
		}
	};
</script>
<style lang=""></style>
