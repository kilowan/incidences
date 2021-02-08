<template>
  <!-- own incidences -->
  <div v-if="checkPermissions(user.permissions, ['6', '7', '8', '9'])">
    <!-- new -->
    <incidences-view v-if="newOwnIncidences"
    :incidences="newOwnIncidences" 
    :title="'Partes nuevos propios'"/>

    <!-- attended -->
    <incidences-view v-if="attendedOwnIncidences"
    :incidences="attendedOwnIncidences" 
    :title="'Partes atendidos propios'"/>

    <!-- closed -->
    <incidences-view v-if="closedOwnIncidences"
    :incidences="closedOwnIncidences" 
    :title="'Partes atendidos propios'"/>

    <!-- hidden -->
    <incidences-view v-if="hiddenOwnIncidences"
    :incidences="hiddenOwnIncidences" 
    :title="'Partes ocultos propios'"/>
  </div>
  <!-- other incidences -->
  <div v-if="checkPermissions(user.permissions, ['10', '11', '12'])">
    <!-- new -->
    <incidences-view v-if="newIncidences"
    :incidences="newIncidences" 
    :title="'Partes nuevos'"/>

    <!-- attended -->
    <incidences-view v-if="attendedIncidences"
    :incidences="attendedIncidences" 
    :title="'Partes atendidos'"/>

    <!-- closed -->
    <incidences-view v-if="closedIncidences"
    :incidences="closedIncidences" 
    :title="'Partes cerrados'"/>
  </div>
</template>

<script>

import incidencesView from './incidencesView.vue';
import axios from 'axios';

export default {
  name: 'incidences',
  props: ['user'],
  components: {
    incidencesView,
  },
  data:function()
  {
    return {
      newOwnIncidences: undefined,
      attendedOwnIncidences: undefined,
      closedOwnIncidences: undefined,
      hiddenOwnIncidences: undefined,
      closedIncidences: undefined,
      attendedIncidences: undefined,
      newIncidences: undefined,
    }
  },
  methods: {
    checkPermissions: function(permissions, permissionNumbers)
    {
      let result = true;
      permissionNumbers.forEach(element => {
        if (!permissions.includes(element)) {
          result = false;
        }
      });
      return result;
    },
  },
  mounted(){
    if (this.checkPermissions(this.user.permissions, ['6', '7', '8', '9'])) {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getOwnNewIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.newOwnIncidences = data.data;
      });
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getOwnIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.attendedOwnIncidences = data.data;
      });
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getOwnOldIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.closedOwnIncidences = data.data;
      });
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getOwnHiddenIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.hiddenOwnIncidences = data.data;
      });
    }
    if (this.checkPermissions(this.user.permissions, ['10', '11', '12'])) {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getNewIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.newIncidences = data.data;
      });
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getOtherIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.attendedIncidences = data.data;
      });
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getOtherOldIncidences&dni=' + this.user.dni,
      }).then(data => {
        this.closedIncidences = data.data;
      });
    }
  }
}
</script>
<style></style>
