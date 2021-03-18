<template>
  <!-- incidenceView -->
  <br />
  <div v-if="menu=='main'">
    <table>
      <tr>
          <th>Datos del parte</th>
      </tr>
    </table><br />
    <table>
      <tr>
        <td>Nombre del empleado</td>
        <td>{{incidence.owner.name}} {{ incidence.owner.surname1 }} {{ incidence.owner.surname2 }}</td>
      </tr>
      <tr>
        <td>Información</td>
        <td v-if="incidence.issueDesc">{{incidence.issueDesc}}</td>
        <td v-else>--</td>
      </tr>
      <tr  v-if="incidence.solver.id">
        <td>Tecnico a cargo</td>
        <td>{{ incidence.solver.name }} {{ incidence.solver.surname1 }} {{ incidence.solver.surname2 }}</td>
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
            <a href="#" @click="change('edit')">Editar</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 1 && (user.permissions.includes('3') || user.permissions.includes('10')) && incidence.owner.id != user.id">
        <td colspan="2">
            <a href="#" @click="change('attend')">Atender</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 2 && (user.permissions.includes('5') || user.permissions.includes('11')) && incidence.owner.id != user.id">
        <td colspan="2">
            <a href="#" @click="change('modify')">Modificar</a>
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
    <div v-if="incidence.pieces">
      <table>
        <tr>
            <th colspan="2">Piezas afectadas</th>
        </tr>
      </table>
      <table>
        <tr v-for="(piece, index) in incidence.pieces" v-bind:key="index">
          <td>{{piece.name}}</td>
        </tr>
      </table>
    </div><br />
    <div v-if="incidence.notes">
      <table>
        <tr>
            <th colspan="2">Notas</th>
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
  <div v-else-if="menu=='edit' && incidence">
    <incidence-module
    :user="user"
    :incidence="incidence"
    :functions="'edit'"
    @reload="reload()"
    @reloadoff="reloadoff()"
    @stepBack="menu='main'"/>
  </div>
  <div v-else-if="menu=='modify' && incidence">
    <incidence-module
    v-if="incidence.owner"
    :user="user" 
    :incidence="incidence"
    :functions="'modify'"
    @reload="reload()"
    @reloadoff="reloadoff()"
    @stepBack="menu='main'"/>
  </div>
  <div v-else>
    <incidence-module
    v-if="checkIncidence()"
    :user="user" 
    :incidence="incidence"
    :functions="'attend'"
    @reload="reload()"
    @reloadoff="reloadoff()"
    @stepBack="menu='main'"/>
  </div>
</template>

<script>

import axios from 'axios';
import incidenceModule from './incidenceModule.vue';

export default {
  name: 'incidencesView',
  props: ['incidence', 'user'],
  components: {
    incidenceModule,
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
    checkIncidence: function()
    {
      return this.incidence.owner ? true : false;
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
    change(mod)
    {
      this.menu = mod;
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
