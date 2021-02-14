<template>
  <!-- modifyIncidence -->
    <table>
      <tr>
        <th colspan="2">Datos del parte</th>
      </tr>
    </table><br />
    <table>
      <tr>
        <td>Nombre del empleado</td>
        <td v-if="incidence">{{incidence.owner.name}} {{incidence.owner.surname1}} {{incidence.owner.surname2}}</td>
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
      <tr>
        <td>Nueva nota</td>
        <td><input type="text" v-model="note" /></td>
      </tr>
      <tr>
        <td>
          <select name="pieza" v-model="selectedPiece">
            <option value="--" selected="selected">--</option>
            <option v-for="(piece, index) in pieces" v-bind:key="index">{{piece.name}}</option>
          </select>
        </td>
        <td><button @click="addPiece()">Añadir</button></td>
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
          <td colspan="2">
            <a href="#" @click="updateIncidence()">Guardar</a>
          </td>
        </tr>
    </table><br />
    <table>
      <tr>
        <th>Piezas afectadas:</th>
      </tr>
    </table>
    <table>
      <tr v-for="(piece, index) in incidence.pieces" v-bind:key="index">
        <td>{{piece.name}}</td>
      </tr>
    </table><br />
    <div v-if="incidence.notes">
    <table>
      <tr>
        <th>Notas anteriores</th>
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
</template>
    
<script>
  import axios from 'axios';

  export default {
    name: 'modifyIncidence',
    props: ['incidence', 'user'],
    components: {
    },
    data:function()
    {
      return {
        //issueDesc: undefined,
        pieces: undefined,
        selected: undefined,
        selectedPiece: undefined,
        funcion: undefined,
        piecesData: undefined,
        PieceIdsSelected: [],
        note: undefined,
        close: false,
      }
    },
    methods: {
    addPiece: function()
    {
      let piece = this.pieces.filter(data =>{
        return data.name == this.selectedPiece;
      })[0];
      
      if (!this.piecesData.includes(piece)) {
        this.piecesData.push(piece);
        this.PieceIdsSelected.push(piece.id);
      }
    },
      updateIncidence: function()
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
    },
    mounted(){
      axios.get("http://localhost:8082/newMenu.php?funcion=getPiecesList")
      .then( data => {
        this.pieces = data.data;
        this.piecesData = this.incidence.pieces;
    });
    }
  }
</script>
<style></style>
