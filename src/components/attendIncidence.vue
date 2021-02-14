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
          <td>{{incidence.owner.name}} {{incidence.owner.surname1}} {{incidence.owner.surname2}}</td>
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
  <table>
      <tr v-if="pieces">
        <th>Lista de piezas:</th>
      </tr>
  </table>
  <table>
      <tr>
        <td>
            <select v-model="selectedPiece" name="pieza">
              <option value="--" selected="true">--</option>
              <option v-for="(piece, index) in pieces" v-bind:key="index">{{ piece.name }}</option>
            </select>
        </td>
        <td><button @click="addPiece()">Añadir</button></td>
      </tr>
  </table><br />
  <table v-if="selectedPieces.length>0">
    <tr>
      <th>Piezas seleccionas:</th>
    </tr>
  </table>
  <table>
    <tr v-for="(selectedPiece, index) in selectedPieces" v-bind:key="index">
      <td v-text="selectedPiece"/>
    </tr>
  </table><br />
  <table>
    <tr>
      <th>Nota</th>
    </tr>
  </table>
  <table>
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
  <a href="#" @click="back()" class="link" center>Atrás</a>
</template>

<script>

import axios from 'axios';

export default {
  name: 'attendIncidence',
  props: ['incidence', 'user'],
  components: {
  },
  data:function()
  {
    return {
      issueDesc: undefined,
      selected: undefined,
      selectedPiece: undefined,
      selectedPieces: [],
      pieces: undefined,
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
            pieces: this.selectedPieces,
          },
          headers: [],
        }).then(
          this.$emit('reload')
        );
      } else {
        this.$emit('reloadoff');
      }
    },
    addPiece: function()
    {
      if (!this.selectedPieces.includes(this.selectedPiece)) {
        this.selectedPieces.push(this.selectedPiece);
      }
    }
  },
  mounted(){
    this.issueDesc = this.incidence.issueDesc;
      axios.get("http://localhost:8082/newMenu.php?funcion=getPiecesList")
      .then( data => {
        this.pieces = data.data;
    });
  }
}
</script>
<style></style>
