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
        <td>{{incidence.owner.name}} {{incidence.owner.surname1}} {{incidence.owner.surname2}}</td>
      </tr>
      <tr>
        <td>Información</td>
        <td v-if="functions=='edit'"><input type="text" name="issueDesc" v-model="issueDesc" required /></td>
        <td v-else>{{ incidence.issueDesc }}</td>
      </tr>
      <tr v-if="incidence.solver.id">
        <td >Tecnico a cargo</td>
        <td>{{ incidence.solver.name }} {{ incidence.solver.surname1 }} {{ incidence.solver.surname2 }}</td>
      </tr>
      <tr>
        <td>Fecha de creación</td>
        <td>{{incidence.initDateTime}}</td>
      </tr>
  </table><br />
      <pieces-module :pieces="incidence.pieces" @add="PieceIdsSelected.push($event)"/>
  <div v-if="functions=='edit'">
    <!-- editIncidence -->
    <table>
      <tr>
        <th>Funciones</th>
      </tr>
    </table>
    <table>
      <tr>
        <td colspan="2" v-if="issueDesc"><a href="#" @click="editIncidence()">Guardar</a></td>
      </tr>
    </table>
  </div>
  <div v-else-if="functions=='attend'">
    <notes-module :notes="incidence.notes" @add="note = $event"/>
    <!-- attendIncidence -->
    <table>
      <tr>
        <th>Funciones</th>
      </tr>
    </table>
    <table>
      <tr>
          <td>
              <a href="#" @click="attendIncidence()">Guardar</a>
          </td>
      </tr>
    </table>
  </div>
  <div v-else-if="functions=='modify'">
  <!-- modifyIncidence -->
    <notes-module :notes="incidence.notes" @add="note = $event"/>
    <table>
      <tr>
        <th>Funciones</th>
      </tr>
    </table>
    <table>
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
import notesModule from './NotesModule.vue';
import piecesModule from './piecesModule.vue';

export default {
  name: 'incidenceModule',
  props: ['incidence', 'user', 'functions'],
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
    }
  },
  mounted: function()
  {
    this.issueDesc = this.incidence.issueDesc;
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
  },
}
</script>
<style></style>
