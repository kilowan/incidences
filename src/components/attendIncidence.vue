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
  <div v-if="selectedPieces.length>0">
    <table>
      <tr>
        <th>Piezas seleccionas:</th>
      </tr>
    </table>
    <table>
      <tr v-for="(selectedPiece, index) in selectedPieces" v-bind:key="index">
        <td v-text="selectedPiece"/>
      </tr>
    </table><br />
  </div>
  <table>
    <tr>
      <th>Nota</th>
    </tr>
  </table>
  <table>
      <tr>
          <td>Nueva nota</td>
          <td><input type="text" v-model="note" /></td>
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
  </table><br />
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
      note: undefined,
      close: false,
      PieceIdsSelected: [],
    }
  },
  methods: {
    updateIncidence: function()
    {
      if (this.selected == 'Cerrar parte') {
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
    addPiece: function()
    {
      if (!this.selectedPieces.includes(this.selectedPiece)) {
        this.selectedPieces.push(this.selectedPiece);
        let piece = this.pieces.filter(data =>{
          return data.name == this.selectedPiece;
        })[0];
        this.PieceIdsSelected.push(piece.id);
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
