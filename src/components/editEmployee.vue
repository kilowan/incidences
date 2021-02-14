<template>
  <!-- editEmployee -->
    <br />
  <table>
    <tr>
        <th>Editar empleado</th>
    </tr>
  </table><br />
  <table>
      <tr>
          <th>DNI</th>
          <th>Nombre</th>
          <th>Primer apellido</th>
          <th>Segundo apellido</th>
          <th>Tipo</th>
          <th>--</th>
      </tr>
      <tr v-if="userData">
          <td><input type="text" name="dni" v-model="userData.dni" required /></td>
          <td><input type="text" name="nombre" v-model="userData.name" required /></td>
          <td><input type="text" name="apellido1" v-model="userData.surname1" required /></td>
          <td><input type="text" name="apellido2" v-model="userData.surname2" /></td>
          <td><input type="text" name="tipo" v-model="userData.tipo" required /></td>
          <td><a href="#" @click="save()">Guardar</a></td>
      </tr>
  </table><br/>
  <a href="#" @click="back()" class="link" center>Atrás</a>
</template>

<script>

import axios from 'axios';

export default {
  name: 'editEmployee',
  props: ['user'],
  components: {
  },
  data:function()
  {
    return {
      userData: undefined
    }
  },
  methods: {
    save()
    {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=updateEmployee&dni=' + this.userData.dni + '&name=' + this.userData.name + '&surname1=' + this.userData.surname1 + '&surname2=' + this.userData.surname2 + '&type=' + this.userData.tipo,
        headers:[],
      }).then(
        this.$emit('reload')
      );
    },
    back:function()
    {
      this.$emit('stepBack');
    }
  },
  mounted(){
    this.userData = this.user;
  }
}
</script>
<style></style>
