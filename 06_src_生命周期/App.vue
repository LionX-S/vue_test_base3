<template>
	<Demo v-if="showDemo" />
	<button @click="showDemoChange">显示/隐藏</button>
</template>

<script>
	import { ref } from "vue";
	import Demo from "./components/Demo.vue";
	export default {
		name: "App",
		components: { Demo },
		setup() {
			let showDemo = ref(true);
			function showDemoChange() {
				showDemo.value = !showDemo.value
			}
			return {
				showDemo,
				showDemoChange
			}
		}
	};
</script>

<style></style>
