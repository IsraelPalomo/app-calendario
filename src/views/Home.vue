<template>
	<div class="home">
		<v-layout :wrap="true">
			<v-flex xs12>
				<v-card>
					<v-date-picker
						full-width
						v-model="fecha"
						locale="es"
						:min="minimo"
						:max="maximo"
						@change="getDolar(fecha)"
					></v-date-picker>
				</v-card>
				<v-card color="error" dark>
					<v-card-text class="display-1 text-center">{{ valor }} </v-card-text>
				</v-card>
			</v-flex>
		</v-layout>
	</div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import { mapMutations } from "vuex";
export default {
	data() {
		return {
			fecha: new Date().toISOString().substr(0, 10),
			minimo: "1999",
			maximo: new Date().toISOString().substr(0, 10),
			valor: null,
		};
	},
	methods: {
		...mapMutations(["mostrarLoading", "ocultarLoading"]),

		async getDolar(dia) {
			let arrayFecha = dia.split(["-"]);
			let ddmmyy = arrayFecha[2] + "-" + arrayFecha[1] + "-" + arrayFecha[0];

			try {
				this.mostrarLoading({ titulo: "Accediendo a Informacion", color: "secondary" });
				const datos = await axios.get(`https://mindicador.cl/api/euro/${ddmmyy}`);
				if (datos.data.serie.length > 0) {
					this.valor = await datos.data.serie[0].valor;
				} else {
					this.valor = "Sin Resultados";
				}

				console.log(datos.data.serie[0].valor);
			} catch (error) {
				console.log(error);
			} finally {
				this.ocultarLoading();
			}
		},
	},
	created() {
		this.getDolar(this.fecha);
	},
};
</script>
