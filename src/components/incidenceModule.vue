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
      <table>
        <tr>
            <th colspan="2">Piezas afectadas:</th>
        </tr>
      </table>
      <table>
        <tr v-for="(piece, index) in incidence.pieces" v-bind:key="index">
          <td>{{piece.name}}</td>
        </tr>
      </table>
    <table>
        <tr v-for="(selectedPiece, index) in selectedPieces" v-bind:key="index">
          <td v-text="selectedPiece"/>
        </tr>
        <tr>
          <td>
              <select v-model="selectedPiece" name="pieza">
                <option value="Selecciona una pieza">Selecciona una pieza</option>
                <option v-for="(piece, index) in pieces" v-bind:key="index">{{ piece.name }}</option>
              </select> <button @click="addPiece()">Añadir</button>
          </td>
        </tr>
    </table><br />
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
    <!-- attendIncidence -->
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
    </table><br />
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
    <div v-if="incidence.notes">
      <table v-if="functions=='modify'">
        <tr>
          <th colspan="2">Notas</th>
        </tr>
        <tr v-for="(note, index) in incidence.notes" v-bind:key="index">
          <td>{{note.noteStr}}</td>
        </tr>
        <tr v-if="addNote">
          <td>
            <input type="text" v-model="note" /> <a href="#" @click="addNote = false">Reiniciar</a>
          </td>
        </tr>
        <tr v-if="!addNote">
          <a href="#" @click="addOn()">Añadir</a>
        </tr>
      </table><br />
    </div>
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

export default {
  name: 'incidenceModule',
  props: ['incidence', 'user', 'functions'],
  components: {
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
      funcion: undefined,
      piecesData: undefined,
      addNote: false,
      checkPiece: false,
    }
  },
  methods: {
    back: function()
    {
      this.$emit('stepBack');
    },
    addOn: function()
    {
      this.addNote = true;
    },
    addPiece: function()
    {
      let piece = this.pieces.filter(data =>{
        return data.name == this.selectedPiece;
      })[0];
      
      let boolean = false;
      this.piecesData.forEach(element => {
        if (element.id == piece.id) {
          boolean = true;
        }
      });
      if (!boolean) {
        this.piecesData.push(piece);
        this.PieceIdsSelected.push(piece.id);
      }
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
  mounted(){
    this.issueDesc = this.incidence.issueDesc;
    axios.get("http://localhost:8082/newMenu.php?funcion=getPiecesList")
    .then( data => {
      this.pieces = data.data;
      this.piecesData = this.incidence.pieces;
    });
  }
}
</script>
<style></style>
