<template>
	<div class="container">
		<b-navbar class="navbar">
			<b-navbar-brand class="">
				<b class=""><b-icon icon="truck"></b-icon> {{ appName }}</b>
			</b-navbar-brand>
		</b-navbar>
		<ContentBox :data="options" />
	</div>
</template>

<script>
import { BNavbar, BNavbarBrand } from "bootstrap-vue"
import axios from "axios"
import ContentBox from "./ContentBox.vue"

export default {
	components: {
		BNavbar,
		BNavbarBrand,
		ContentBox,
	},
	data() {
		return {
			appName: "Melhor Frete",
			options: [],
			selectedCity: null,
		}
	},
	methods: {
		async getAPI() {
			try {
				const response = await axios.get("http://localhost:3000/transport")
				this.options = response.data
			} catch (error) {
				console.error(error)
			}
		},
		async fetchTransportData(city) {
			try {
				const response = await axios.get("http://localhost:3000/transport")
				const filteredOptions = response.data.filter((option) => option.city === city)
				console.log(`Dados de transporte para: ${city}`, filteredOptions)
			} catch (error) {
				console.error(error)
			}
		},
	},
	created() {
		this.getAPI()
	},
}
</script>

<style scoped>
.container {
	padding-right: 0;
	padding-left: 0;
	border-radius: 10px;
	background: #fffafa;
	margin-top: 2rem;
	box-shadow: 0px 0px 5px 3px rgba(0, 0, 0, 0.75);
}

.navbar {
	background-color: #9eb7e5;
	width: 100%;
	padding: 1rem 2rem;
}
</style>
