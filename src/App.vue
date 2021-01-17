<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <p v-if="response.permissions"><HelloWorld msg='Welcome to Your Vue.js App'/> {{response.name}}</p>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import axios from 'axios';

export default {
  name: 'App',
  components: {
    HelloWorld,
  },
  data:function()
  {
    return {
      Id: 3,
      response: {
        name: "",
        surname1: "",
        surname2: "",
        dni: "",
        tipo: "",
        id: undefined,
        permissions: [],
        borrado: 0,
      },
    }
  },
  mounted:function()
  {
    axios.get("http://90.68.35.159:8081/newMenu.php?funcion=getEmployeeById&id="+ this.Id)
    .then( data => {
      this.response = data.data;
      console.log(this.response.name);
    });
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
