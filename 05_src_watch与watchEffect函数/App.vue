<template>
	<Demo/>
</template>

<script>
	import Demo from "./components/Demo.vue";
	export default {
		name: "App",
		components: { Demo },
		setup() {
			function helloFun(value) {
				console.log("触发了自定义事件，收到的值为" + value);
			}

			return {
				helloFun
			}
		}
	};
</script>

<style></style>
