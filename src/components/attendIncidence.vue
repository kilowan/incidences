<template>
  <!-- attendIncidence -->
  <br /><table>
      <tr>
          <th>Editar Parte</th>
      </tr>
  </table><br />
  <table>
      <tr>
          <td>Nombre del empleado</td>
          <td>{{incidence.owner.name}} {{incidence.owner.surname1}} {{$incidence.owner.surname2}}</td>
      </tr>
      <tr>
          <td>Información</td>
          <td v-if="incidence.issueDesc">{{incidence.issueDesc}}</td>
      </tr>
      <tr>
          <td>Tecnico a cargo</td>
          <td v-if="incidence.solver">{{incidence.solver.id}}</td>
      </tr>
      <tr>
          <td>Fecha de creación</td>
          <td>{{incidence.initDateTime}}</td>
      </tr>
  </table><br />
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
  <table>
    <tr>
        <th>Piezas afectadas</th>
    </tr>
    <tr v-for="(piece, index) in incidence.pieces" v-bind:key="index">
        <td>Nombre</td>
        <td v-if="piece.name">{{piece.name}}</td>
    </tr>
    <tr>
        <td>Descripcion</td>
        <td v-if="piece.description">{{piece.description}}</td>
    </tr>
  </table>
  <table>
      <tr>
          <td>Piezas afectadas:</td>
          <td>
              <select v-model="selectedPiece" name="pieza">
                <option value="--" selected="selected">--</option>
                <option v-for="(piece, index) in piecesList" v-bind:key="index"/>
              </select>
          </td>
      </tr>
      <tr>
          <td>Nueva nota</td>
          <td><input type="text" name="not_tec" /></td>
      </tr>
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
          <td>
              <a href="#" @click="updateIncidence()">Guardar</a>
          </td>
      </tr>
  </table>
</template>

<script>

import axios from 'axios';

export default {
  name: 'editIncidence',
  props: ['incidence', 'user'],
  components: {
  },
  data:function()
  {
    return {
      issueDesc: undefined,
      selected: undefined,
      selectedPiece: undefined,
    }
  },
  methods: {
    updateIncidence: function()
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
  mounted(){
    this.issueDesc = this.incidence.issueDesc;
  }
}
</script>
<style></style>
