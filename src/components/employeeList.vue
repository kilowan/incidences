<template>
  <!-- employeeList -->
  <div id="employeList" v-if="mod=='employeeList'">
    <br /><table>
        <tr>
            <th>Lista de empleados</th>
        </tr>
    </table><br />
    <table>
        <tr>
            <th>ID de empleado</th>
            <th>DNI del empleado</th>
            <th>Nombre</th>
            <th>Primer apellido</th>
            <th>Segundo apellido</th>
            <th>Tipo de empleado</th>
            <th colspan="3">--</th>
        </tr>
        <tr v-for="(employee, index) in employees" v-bind:key="index">
            <td><a href="#" @click="panel(employee)">{{employee.id}}</a></td>
            <td>{{employee.dni}}</td>
            <td>{{employee.name}}</td>
            <td>{{employee.surname1}}</td>
            <td>{{employee.surname2}}</td>
            <td>{{employee.tipo}}</td>
            <td><a href="veremp.php?id={{employee.id}}&funcion=Borrar_empleado">Borrar</a></td>
            <td><a href="veremp.php?funcion=Editar_empleado&id_emp={{employee.id}}&dni={{employee.dni}}">Editar</a></td>
        </tr>
        <tr>
          <td colspan="8">
              <a href="veremp.php?funcion=Agregar_empleado&id_emp={{employee.id}}&dni={{employee.dni}}">Agregar nuevo</a>
          </td>
        </tr>
    </table>
  </div>
  <div v-else id="panel">
    <user-panel :user="employee"/>
  </div>
</template>

<script>

import axios from 'axios';
import UserPanel from './userPanel.vue';

export default {
  name: 'userInfo',
  props: ['user'],
  components: {
    UserPanel
  },
  data:function()
  {
    return {
      employees: undefined,
      employee: undefined,
      mod: 'employeeList'
    }
  },
  methods: {
    panel: function(employee)
    {
      this.employee = employee;
      this.mod = 'panel';
    },
  },
  mounted(){
    axios({
      method: 'get',
      url: 'http://localhost:8082/newMenu.php?funcion=getEmpolyeeList',
    })
    .then(data =>
      this.employees = data.data
    );
  }
}
</script>
<style></style>
