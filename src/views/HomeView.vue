<template>
<div>
  <v-row>
    <v-col md="12" >
      <v-card>
        <v-date-picker 
        v-model="picker"
        full-width
        locale="es"
        :min="minimo"
        :max="maximo"
        @change=" getDolar(picker)"
        >
        
        </v-date-picker>
      </v-card>
      <v-card color="error" dark>
        <v-card-text class="display-1 text-md-center text-sm-center" >
          {{valor}}
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</div>
</template>

<script> 
  import axios from 'axios';
  import { mapMutations } from 'vuex';
  export default {
    data(){
      return{
        picker: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
        minimo:'1984',
        maximo:new Date().toISOString().substr(0, 10),
        valor: 0
      }
    },
    methods:{
      ...mapMutations(['mostrarLoading','ocultarLoading']),
      async getDolar(dia){
        let arrayFecha= dia.split('-').reverse().join('-')
        try{

          this.mostrarLoading({titulo:'Accediendo a informacion', color:'secondary'})
          let datos= await axios.get(`https://mindicador.cl/api/dolar/${arrayFecha}`);

          if(datos.data.serie.length > 0){
            this.valor= await datos.data.serie[0].valor;
          }else{
            this.valor= "sin resultados"
          }
        }catch(error){
          console.log(error)
        }finally{
          this.ocultarLoading()
        }
 
      }
    },
    created(){
      this.getDolar(this.picker)
    }
  }
</script>
