<template>
  <!-- userInfo -->
  <br /><table>
    <tr>
      <th>Datos personales</th>
    </tr>
  </table><br />
  <table>
    <tr>
      <td>Id Empleado</td>
      <td>{{ user.id }}</td>
    </tr>
    <tr>
      <td>DNI</td>
      <td>{{ user.dni }}</td>
    </tr>
    <tr>
      <td>Nombre</td>
      <td v-if="!editName" @click="editname()">{{ user.name }}</td>
      <td v-if="editName">
        <input type="text" v-model="name"/>
      </td>
    </tr>
    <tr>
      <td>Primer apellido</td>
      <td v-if="!editSurname1" @click="editsurname1()">{{ user.surname1 }}</td>
      <td v-if="editSurname1">
        <input type="text" v-model="surname1"/>
      </td>
    </tr>
    <tr>
      <td>Segundo apellido</td>
      <td v-if="!editSurname2" @click="editsurname2()">{{ user.surname2 }}</td>
      <td v-if="editSurname2">
        <input type="text" v-model="surname2"/>
      </td>
    </tr>
    <tr>
      <td>Tipo</td>
      <td>{{ user.tipo }}</td>
    </tr>
    <tr v-if="editSurname2 || editSurname1 || editName">
      <td colspan="2"><a href="#" @click="saveData()">Guardar</a></td>
    </tr>
  </table><br />
</template>

<script>

import axios from 'axios';

export default {
  name: 'userInfo',
  props: ['user'],
  components: {
  },
  data:function()
  {
    return {
      editName: false,
      editSurname1: false,
      editSurname2: false,
      name: undefined,
      surname1: undefined,
      surname2: undefined,
      fields: [],
      values: [],
    }
  },
  methods: {
    editname: function()
    {
      this.name = this.user.name;
      this.editName = true;
    },
    editsurname1: function()
    {
      this.surname1 = this.user.surname1;
      this.editSurname1 = true;
    },
    editsurname2: function()
    {
      this.surname2 = this.user.surname2;
      this.editSurname2 = true;
    },
    checkField(field, field2)
    {
      return field && field != field2? true: false
    },
    fillData(data)
    {
      if(this.checkField(data[0], this.user.name))
      {
        this.values.push(data[0]);
        this.fields.push("nombre");
      }
      if(this.checkField(data[1], this.user.surname1))
      {
        this.values.push(data[1]);
        this.fields.push("apellido1");
      }
      if(this.checkField(data[2], this.user.surname2))
      {
        this.values.push(data[2]);
        this.fields.push("apellido2");
      }
    },
    saveData: function()
    {
      this.fillData([this.name, this.surname1, this.surname2]);
      if (this.fields.length >0) 
      {
        axios({
          method: 'post',
          url: 'http://localhost:8082/newMenu.php',
          data: {
            funcion: 'updateWorker',
            dni: this.user.dni,
            fields: this.fields,
            values: this.values,
          },
          headers:[],
        }).then(
          this.$emit('reload')
        );        
      }
      this.$emit('reloadUser', this.user.dni);
      this.editName = false;
      this.editSurname1 = false;
      this.editSurname2 = false;
      this.name = undefined;
      this.surname1 = undefined;
      this.surname2 = undefined;
      this.fields = [];
      this.values = [];
    },
  },
  mounted(){}
}
</script>
<style></style>
