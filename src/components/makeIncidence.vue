<template>
  <!-- MakeIncidence -->
    <b-form class="crearP">
        <label>Descripción del problema:</label><br />
        <textarea name="descripcion" rows="10" cols="40" required placeholder="resumen del fallo"></textarea><br />
        <p> ¿Que pieza crees que falla?:</p>
        <p> 
            <select v-model="selected" name="pieza" required>
                <option value="--" selectted="selected">--</option>
                <option value="nose">No lo se</option>
                <option value="torre">La torre</option>
                <option value="pantalla">El monitor o proyector</option>
                <option value="raton">El raton</option>
                <option value="teclado">El teclado</option>
                <option value="impresora">La impresora</option>
            </select>
        </p>
        <b-button @click="addIncidence()" name="Submit" type="submit">Crear parte</b-button>
    </b-form><br/>
</template>

<script>
import axios from 'axios';
//import Vue from "vue";
//import VueRouter from 'vue-router'

/*const routes = [
  {
    path: "/",
    name: "home",
    //component: Home
  },
];*/

/*var router = new VueRouter({
  mode: "history",
  //routes
});*/

export default {
  name: 'makeincidence',
  components: {
    //Vue,
    //VueRouter,
  },
  data:function()
  {
    return {
      selected: undefined,
      pieces: [],
    }
  },
  methods: {
    addIncidence: function()
    {
      axios.get("http://localhost:8082/newMenu.php?funcion=getPiecesList")
      .then( data => {
        this.pieces = data.data;
        this.showIncidences();
      });
    },
  },
  mounted:function(){}
}
</script>

<style></style>
