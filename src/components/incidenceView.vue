<template>
  <!-- incidenceView -->
  <br />
  <div v-if="menu=='main'">
    <table>
      <tr>
          <th>Ver Parte</th>
      </tr>
    </table><br />
    <table>
      <tr>
        <td>Nº parte</td>
        <td v-if="incidence.id">{{incidence.id}}</td>
        <td v-else>--</td>
      </tr>
      <tr>
        <td>Empleado</td>
        <td v-if="incidence.owner.id">{{incidence.owner.id}}</td>
        <td v-else>--</td>
      </tr>
      <tr>
        <td>Información</td>
        <td v-if="incidence.issueDesc">{{incidence.issueDesc}}</td>
        <td v-else>--</td>
      </tr>
      <tr>
        <td>Tecnico a cargo</td>
        <td v-if="incidence.solver.id">{{incidence.solver.id}}</td>
        <td v-else>--</td>
      </tr>
      <tr>
        <td>Fecha de creación</td>
        <td v-if="incidence.initDateTime">{{incidence.initDateTime}}</td>
        <td v-else>--</td>
      </tr>
      <tr v-if="incidence.state == 1 && user.permissions.includes('6') && incidence.owner.id == user.id">
        <td>
            <a href="#" @click="deleteIncidence()">Borrar</a>
        </td>
        <td>
            <a href="#" @click="editIncidence()">Editar</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 1 && (user.permissions.includes('3') || user.permissions.includes('10')) && incidence.owner.id != user.id">
        <td colspan="2">
            <a href="veremp.php?id_emp={{user.id}}&dni={{user.dni}}&id_part={{incidence.id}}&funcion=Atender_parte">Atender</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 2 && (user.permissions.includes('5') || user.permissions.includes('11')) && incidence.owner.id != user.id">
        <td colspan="2">
            <a href="veremp.php?funcion=Modificar_parte&id_emp={{user.id}}&dni={{user.dni}}&id_part={{incidence.id}}">Modificar</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 3 && user.permissions.includes('22')">
        <td colspan="2">
            <a href="#" @click="hide()">Ocultar</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 4 && user.permissions.includes('9')">
        <td colspan="2">
            <a href="#" @click="show()">Mostrar</a>
        </td>
      </tr>
    </table><br />
    <div v-if="incidence.notes">
      <table>
        <tr>
            <th colspan="2">Notas del ténico</th>
        </tr>
      </table><br />
      <table>
        <tr>
            <th>Nota</th>
            <th>Fecha</th>
        </tr>
        <tr v-for="(note, index) in incidence.notes" v-bind:key="index">
            <td>{{note.noteStr}}</td>
            <td>{{note.date}}</td>
        </tr>
      </table><br />
    </div>
    <a href="#" @click="back()" class="link">Atrás</a>
  </div>
  <div v-else-if="menu=='edit'">
    <edit-incidence
    :user="user"
    :incidence="incidence"
    @reload="reload()"
    @reloadoff="reloadoff()"/>
  </div>
  <div v-else>
    <attend-incidence 
    :user="user" 
    :incidence="incidence"
    @reload="reload()"
    @reloadoff="reloadoff()"/>
  </div>
</template>

<script>

import axios from 'axios';
import editIncidence from './editIncidence.vue';
import attendIncidence from './attendIncidence.vue';

export default {
  name: 'incidencesView',
  props: ['incidence', 'user'],
  components: {
    editIncidence,
    attendIncidence,
  },
  data:function()
  {
    return {
      menu: 'main',
    }
  },
  methods: {
    back: function()
    {
      this.$emit('stepBack');
    },
    check: function()
    {
      return Object.keys(this.incidences).length >0;
    },
    hide: function()
    {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=hideIncidence&incidenceId=' + this.incidence.id + '&userId=' + this.user.id,
      }).then(data => {
        this.$emit('reload', data);
        //this.hiddenOwnIncidences = data.data;
      });
    },
    show: function()
    {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=showIncidence&incidenceId=' + this.incidence.id + '&userId=' + this.user.id,
      }).then(data => {
        this.$emit('reload', data);
        //this.hiddenOwnIncidences = data.data;
      });
    },
    editIncidence: function()
    {
      this.menu = 'edit';
    },
    attendIncidence: function()
    {
      this.menu = 'attend';
    },
    deleteIncidence: function()
    {
      axios({
          method: 'get',
          url: 'http://localhost:8082/newMenu.php?funcion=deleteIncidence&incidenceId=' + this.incidence.id + '&userId=' + this.user.id,
        }).then(
          this.$emit('reload')
        );
    },
    reload:function()
    {
      this.manu='main';
      this.$emit('reload');
    },
    reloadoff:function()
    {
      this.manu='main';
    }
  },
  mounted(){}
}
</script>
<style></style>
