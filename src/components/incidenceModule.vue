<template>
  <table>
    <tr>
      <th>Datos del parte</th>
    </tr>
  </table><br />
  <!-- Datos del parte -->
  <table>
      <tr>
        <td>Nombre del empleado</td>
        <td v-if="owner">{{owner.name}} {{owner.surname1}} {{owner.surname2}}</td>
      </tr>
      <tr>
        <td>Información</td>
        <td v-if="incidence.state == 1 && user.permissions.includes('6') && owner.id == user.id"><input type="text" name="issueDesc" v-model="issueDesc" required /></td>
        <td v-else>{{ incidence.issueDesc }}</td>
      </tr>
      <tr v-if="solver.name">
        <td >Tecnico a cargo</td>
        <td>{{ incidence.solver.name }} {{ solver.surname1 }} {{ solver.surname2 }}</td>
      </tr>
      <tr>
        <td>Fecha de creación</td>
        <td>{{incidence.initDateTime}}</td>
      </tr>
      <tr v-if="incidence.state == 1 && user.permissions.includes('6') && owner.id == user.id">
        <td>
            <a href="#" @click="deleteIncidence()">Borrar</a>
        </td>
        <td>
            <a href="#" @click="edit=true">Editar</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 1 && (user.permissions.includes('3') || user.permissions.includes('10')) && owner.id != user.id">
        <td colspan="2">
            <a href="#" @click="edit=true">Atender</a>
        </td>
      </tr>
      <tr v-else-if="incidence.state == 2 && (user.permissions.includes('5') || user.permissions.includes('11')) && owner.id != user.id">
        <td colspan="2">
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
      <pieces-module v-if="incidence.pieces" :edit="edit" :pieces="incidence.pieces" @add="PieceIdsSelected.push($event)"/>
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
  <div v-else-if="incidence.state == 1 && (user.permissions.includes('3') || user.permissions.includes('10')) && owner.id != user.id">
    <notes-module v-if="incidence.notes" :edit="edit" :notes="incidence.notes" @add="note = $event"/>
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
  <div v-else-if="incidence.state == 2 && (user.permissions.includes('5') || user.permissions.includes('11')) && owner.id != user.id">
  <!-- modifyIncidence -->
    <notes-module :edit="edit" :notes="incidence.notes" @add="note = $event"/>
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
  name: 'incidenceModule',
  props: ['incidence', 'user'],
  components: {
    notesModule,
    piecesModule,
  },
  data:function()
  {
    return {
      issueDesc: undefined,
      selected: undefined,
      selectedPiece: 'Selecciona una pieza',
      selectedPieces: [],
      pieces: undefined,
      note: undefined,
      close: false,
      PieceIdsSelected: [],
      piecesData: undefined,
      edit: false,
      owner: undefined,
      solver: undefined,
    }
  },
  mounted: function()
  {
    this.issueDesc = this.incidence.issueDesc;
    this.owner = this.incidence.owner;
    this.solver = this.incidence.solver;
    axios.get("http://localhost:8082/newMenu.php?funcion=getPiecesList")
    .then( data => {
      this.pieces = data.data;
      this.piecesData = this.incidence.pieces;
    });
  },
  methods: {
    back: function()
    {
      this.$emit('stepBack');
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
    deleteIncidence: function()
    {
      axios({
          method: 'get',
          url: 'http://localhost:8082/newMenu.php?funcion=deleteIncidence&incidenceId=' + this.incidence.id + '&userId=' + this.user.id,
        }).then(
          this.$emit('reload')
        );
    },
  },
}
</script>
<style></style>
