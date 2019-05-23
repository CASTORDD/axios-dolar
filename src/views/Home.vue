<template>
<div>
  <v-layout :wrap="true">
    <v-flex xs12>
      <v-card>
        <v-date-picker 
          v-model="fecha"
          full-width
          locale="pt"
          :min="minDate"
          :max="maxDate"
          @change="getDolar( fecha )"
        ></v-date-picker>
      </v-card>
      <v-card color="error" dark>
        <v-card-text class="display-1 text-xs-center">
          {{ valor }} - {{ fecha }}
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</div>
</template>

<script>

import axios from 'axios'
import mapMutations from 'vuex'

export default {
  data(){
    return {
      fecha: new Date().toISOString().substr(0, 10),
      minDate: '1984',
      maxDate: new Date().toISOString().substr(0, 10),
      valor: null
    }
  },
  methods: {
    ...mapMutations(['mostrarLoading','ocultarLoading']),

    async getDolar( dia ){
      let arrayDate = dia.split('-')
      let ddmmyy = arrayDate[2] + '-' + arrayDate[1] + '-' + arrayDate[0]
      
      try {

        this.mostrarLoading({
          titulo: 'Accediendo a informcion', 
          color: 'secundary'
        })

        let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)

        if(datos.data.serie.length > 0){
          this.valor = await datos.data.serie[0].valor
        } else {
          this.valor = 'Sin registros'
        }
        console.log(datos.data.serie[0].valor)
        
      } catch (error) {
        // console.log(error)
      } finally {
        this.ocultarLoading()
      }

    }
  },
  created() {
    this.getDolar(this.fecha)
  }
}
</script>
