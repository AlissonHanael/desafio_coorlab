<template>
	<div class="content">
		<div class="form">
			<b-form @submit.prevent="submitForm">
				<h1 class="title"><b-icon icon="map"></b-icon> Insira o destino e o peso</h1>
				<b-form-group class="col-md-10" id="destino" label="Destino">
					<b-form-select size="" class="form-select" v-model="selected">
						<option value="" selected disabled>Selecione o destino</option>
						<option v-for="city in uniqueCities" :key="city" :value="city">
							{{ city }}
						</option>
					</b-form-select>
				</b-form-group>

				<b-form-group class="col-md-10" id="peso" label="Peso">
					<b-form-input v-model="weight" placeholder="Peso da carga em kg"></b-form-input>
				</b-form-group>

				<div class="col-md-10 botao">
					<b-button class="botao--alt" type="submit">Analisar</b-button>
				</div>
			</b-form>
		</div>

		<div class="result" v-if="formSubmitted">
			<h2>Estas são as melhores alternativas de frete para você.</h2>
			<div v-if="weight" class="frete">
				<b-icon icon="cash-coin" class="frete--icon" scale="5"></b-icon>
				<div class="frete--dados">
					<h3>Frete com menor valor</h3>
					<p>Transportadora: {{ precoPorPeso.opcaoMenorPreco.name }}</p>
					<p>Tempo de entrega: {{ precoPorPeso.opcaoMenorPreco.lead_time }}</p>
				</div>
				<div class="frete--preco">
					<h3>Preço</h3>
					<p>R${{ precoPorPeso.preco }}</p>
				</div>
			</div>
			<div v-if="menorLeadTime" class="frete">
				<b-icon icon="clock-history" class="frete--icon" scale="5"></b-icon>
				<div class="frete--dados">
					<h3>Frete mais rápido</h3>
					<p>Transportadora: {{ menorLeadTime.menorLeadTimeOption.name }}</p>
					<p>Tempo estimado: {{ menorLeadTime.menorLeadTimeOption.lead_time }}</p>
				</div>
				<div class="frete--preco">
					<h3>Preço</h3>
					<p>R$ {{ menorLeadTime.preco }}</p>
				</div>
			</div>
			<div class="col-md-10 botao">
				<b-button class="botao--alt" type="submit" @click="resetForm">Limpar</b-button>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	props: {
		data: {
			type: Array,
			required: true,
		},
	},

	data() {
		return {
			selected: "",
			weight: null,
			result: null,
			formSubmitted: false,
			preco: null,
		}
	},
	computed: {
		uniqueCities() {
			return [...new Set(this.data.map((data) => data.city))]
		},
		filteredOptions() {
			return this.selected ? this.data.filter((data) => data.city === this.selected) : []
		},
		menorLeadTime() {
			if (this.filteredOptions.length > 0 && this.weight > 0) {
				const peso = parseFloat(this.weight)
				let preco = 0
				let menorLeadTimeOption = this.filteredOptions[0]
				//itera pelo tamanho da lista de opcoes e retorna o de menor tempo
				for (let i = 0; i < this.filteredOptions.length; i++) {
					const option = this.filteredOptions[i]
					const menorLead = parseInt(menorLeadTimeOption.lead_time.replace("h", ""))
					const optionLead = parseInt(option.lead_time.replace("h", ""))
					if (menorLead > optionLead) {
						menorLeadTimeOption = this.filteredOptions[i]
						const costTransport =
							peso <= 100
								? parseFloat(menorLeadTimeOption.cost_transport_light.replace("R$ ", ""))
								: parseFloat(menorLeadTimeOption.cost_transport_heavy.replace("R$ ", ""))
						preco = peso * costTransport
					}
				}
				return {
					menorLeadTimeOption,
					preco: preco.toFixed(2),
				}
			} else {
				return null
			}
		},

		precoPorPeso() {
			if (this.filteredOptions.length > 0 && this.weight > 0) {
				const peso = parseFloat(this.weight)
				let menorPreco = Infinity
				let opcaoMenorPreco = null

				for (let i = 0; i < this.filteredOptions.length; i++) {
					const costTransport =
						peso <= 100
							? parseFloat(this.filteredOptions[i].cost_transport_light.replace("R$ ", ""))
							: parseFloat(this.filteredOptions[i].cost_transport_heavy.replace("R$ ", ""))
					const preco = peso * costTransport
					if (preco < menorPreco) {
						menorPreco = preco
						opcaoMenorPreco = this.filteredOptions[i]
					}
				}
				console.log(opcaoMenorPreco)

				return {
					opcaoMenorPreco: opcaoMenorPreco,
					preco: menorPreco.toFixed(2), // Arredondando para 2 casas decimais
				}
			} else {
				return null
			}
		},
	},
	methods: {
		submitForm() {
			if (!this.selected || !this.weight) {
				alert("Por favor, selecione o destino e insira o peso.")
				return
			}
			this.formSubmitted = true
		},
		resetForm() {
			this.selected = ""
			this.weight = null
			this.result = null
			this.formSubmitted = false
		},
	},
	watch: {
		selectedCity(newCity) {
			console.log(`Cidade selecionada: ${newCity}`)
			this.fetchTransportData(newCity)
		},
	},
}
</script>

<style scoped>
.title {
	font-size: 2rem;
}
.content {
	border-radius: 10px;
	width: auto;
	display: flex;
	justify-content: space-between;
}

.form {
	border-radius: 10px;
	width: 45%;
	height: 50vh;
	background-color: #e1e1e1;
	display: flex;
	justify-content: center;
	padding: 2rem;
	margin: 0.625rem 0 2rem 2rem;
}

.botao {
	display: flex;
	align-items: center;
	justify-content: center;
	margin-top: 0.825rem;
}
.botao--alt {
	background-color: #9eb7e5;
	border: none;
}
.botao--alt:hover {
	background-color: #7d96ff;
}

.result {
	margin: 0.625rem 2rem 2rem 0;
	padding-left: 2rem;
}

.frete {
	display: flex;
	justify-content: space-evenly;
	background-color: #e1e1e1;
	margin-top: 15px;
	border-radius: 10px;
}
.frete--icon {
	height: auto;
	display: flex;
	justify-content: center;
	align-items: center;
}
.frete--dados {
	margin-top: 0.85rem;
}
.frete--preco {
	margin-top: 0.85rem;
}
</style>
