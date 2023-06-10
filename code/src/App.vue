<template>
	<div id="app">
		<HeaderTransport />
		<BestTransport />
		<ContentBox />
	</div>
</template>

<script>
import HeaderTransport from "./components/HeaderTransport.vue"
import BestTransport from "./components/BestTransport.vue"
import ContentBox from "./components/ContentBox.vue"

export default {
	name: "App",
	components: {
		HeaderTransport,
		BestTransport,
		ContentBox,
	},
}
</script>

<style></style>
