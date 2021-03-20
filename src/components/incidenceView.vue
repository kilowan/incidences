<template>
  <!-- incidenceView -->
  <br />
  <table>
    <tr>
      <th>Datos del parte</th>
    </tr>
  </table><br />
  <!-- Datos del parte -->
  <table>
      <tr v-if="incidence.owner.id != null">
        <td>Nombre del empleado</td>
        <td >{{incidence.owner.name}} {{incidence.owner.surname1}} {{incidence.owner.surname2}}</td>
      </tr>
      <tr>
        <td>Información</td>
        <td v-if="incidence.state == 1 && user.permissions.includes('6') && incidence.owner.id == user.id"><input type="text" name="issueDesc" v-model="issueDesc" required /></td>
        <td v-else>{{ incidence.issueDesc }}</td>
      </tr>
      <tr v-if="incidence.solver.id != null">
        <td >Tecnico a cargo</td>
        <td>{{ incidence.solver.name }} {{ incidence.solver.surname1 }} {{ incidence.solver.surname2 }}</td>
      </tr>
      <tr>
        <td>Fecha de creación</td>
        <td>{{incidence.initDateTime}}</td>
      </tr>
      <tr v-if="incidence.state == 1 && user.permissions.includes('6') && incidence.owner.id == user.id">
        <td>
            <a href="#" @click="deleteIncidence()">Borrar</a>
        </td>
        <td v-if="!edit">
            <a href="#" @click="edit=true">Editar</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 1 && (user.permissions.includes('3') || user.permissions.includes('10')) && incidence.owner.id != user.id">
        <td colspan="2" v-if="!edit">
            <a href="#" @click="edit=true">Atender</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 2 && (user.permissions.includes('5') || user.permissions.includes('11')) && incidence.owner.id != user.id">
        <td colspan="2" v-if="!edit">
            <a href="#" @click="edit=true">Modificar</a>
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
    <pieces-module :edit="edit" :pieces="incidence.pieces" @add="PieceIdsSelected.push($event)"/>
    <notes-module v-if="incidence.notes || edit" :edit="edit" :notes="incidence.notes" @add="note = $event"/>
  <div v-if="incidence.state == 1 && user.permissions.includes('6') && owner.id == user.id">
    <!-- editIncidence -->
    <table v-if="edit">
      <tr>
        <th>Funciones</th>
      </tr>
    </table>
    <table v-if="edit">
      <tr>
        <td colspan="2" v-if="issueDesc && edit"><a href="#" @click="editIncidence()">Guardar</a></td>
      </tr>
    </table>
  </div>
  <div v-else-if="incidence.state == 1 && (user.permissions.includes('3') || user.permissions.includes('10')) && incidence.owner.id != user.id">
    <!-- attendIncidence -->
    <table v-if="edit">
      <tr>
        <th>Funciones</th>
      </tr>
    </table>
    <table v-if="edit">
      <tr>
          <td>
              <a href="#" @click="attendIncidence()">Guardar</a>
          </td>
      </tr>
    </table>
  </div>
  <div v-else-if="incidence.state == 2 && (user.permissions.includes('5') || user.permissions.includes('11')) && incidence.owner.id != user.id">
  <!-- modifyIncidence -->
    <table v-if="edit">
      <tr>
        <th>Funciones</th>
      </tr>
    </table>
    <table v-if="edit">
        <tr>
          <td>Función</td>
          <td>
            <select v-model="selected">
              <option value="insertparte">Actualizar parte</option>
              <option value="cierraparte">Cerrar parte</option>
            </select>
          </td>
        </tr>
        <tr>
          <td colspan="2">
            <a href="#" @click="modifyIncidence()">Guardar</a>
          </td>
        </tr>
    </table><br />
  </div>
<br /><a href="#" @click="back()" class="link" center>Atrás</a>
</template>

<script>

import axios from 'axios';
import notesModule from './notesModule.vue';
import piecesModule from './piecesModule.vue';
export default {
  name: 'incidencesView',
  props: ['incidence', 'user'],
  components: {
    notesModule,
    piecesModule,
  },
  data:function()
  {
    return {
      menu: 'main',
      incidenceData: [],
      issueDesc: undefined,
      selected: undefined,
      pieces: undefined,
      note: undefined,
      edit: false,
      PieceIdsSelected: [],
      close: false,
    }
  },
  methods: {
    back: function()
    {
      this.$emit('stepBack');
    },
    stepBack: function()
    {
      this.load();
      this.$emit('stepBack');
    },
    check: function()
    {
      return Object.keys(this.incidences).length >0;
    },
    load: function()
    {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getIncidenceById&id_part=' + this.incidence.id,
      }).then(data => {
        this.incidenceData = data.data;
      });
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
    },
    editIncidence: function()
    {
      if (this.incidence.issueDesc != this.issueDesc) {
        axios({
          method: 'post',
          url: 'http://localhost:8082/newMenu.php',
          data: {
            funcion: 'updateNotes',
            incidenceId: this.incidence.id,
            incidenceDesc: this.issueDesc,
            employeeId: this.user.id,
          },
          headers: [],
        }).then(
          this.$emit('reload')
        );
      } else {
        this.$emit('reloadoff');
      }
    },
    attendIncidence: function()
    {
      if (this.selected == 'cierraparte') {
        this.close = true;
      }
        axios({
          method: 'post',
          url: 'http://localhost:8082/newMenu.php',
          data: {
            funcion: 'updateIncidence',
            incidenceId: this.incidence.id,
            userId: this.user.id,
            note: this.note,
            pieces: this.PieceIdsSelected,
            close: this.close,
          },
          headers: [],
        }).then(
          this.$emit('reload')
        );
    },
      modifyIncidence: function()
      {
        if (this.selected == 'cierraparte') {
          this.close = true;
        }
        axios({
          method: 'post',
          url: 'http://localhost:8082/newMenu.php',
          data: {
            funcion: 'updateIncidence',
            incidenceId: this.incidence.id,
            userId: this.user.id,
            note: this.note,
            pieces: this.PieceIdsSelected,
            close: this.close,
          },
          headers: [],
        }).then(this.$emit('reload'));
      },
  },
  mounted(){
    this.load();
  }
}
</script>
<style></style>
