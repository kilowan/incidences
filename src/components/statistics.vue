<template>
  <!-- globalstatistics -->
  <div id="globalStatistics">
    <table>
        <tr>
            <th colspan="2">Estadisticas globales</th>
        </tr>
    </table><br />
    <table>
      <tr>
          <th>Tiempo medio</th>
          <th>Nombre de empleado</th>
      </tr>
      <tr>
          <td>prueba1</td>
          <td>prueba2</td>
      </tr>
    </table><br />
  </div>
<!-- reportedPieces -->
<div v-if="pieces" id="reportedPieces">
  <table>
    <tr>
        <th colspan="2">Piezas reportadas</th>
    </tr>
    <tr>
        <th>Nombre</th>
        <th>Nº de reportes</th>
    </tr>
    <tr v-for="(piece, index) in pieces" v-bind:key="index">
        <td v-text="piece.pieceName"></td>
        <td v-text="piece.pieceNumber" ></td>
    </tr>
  </table><br />
  </div>
  <!-- statistics -->
  <div v-if="statistics" id="statistics">
    <table>
      <tr>
          <th colspan="2">Estadisticas</th>
      </tr>
    </table><br />
    <table>
        <tr>
            <th>Tiempo medio</th>
            <th>Partes resueltos</th>
        </tr>
        <tr>
            <td>{{ statistics.average }}</td>
            <td>{{ statistics.solvedIncidences }}</td>
        </tr>
    </table><br />
  </div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'statistics',
  props: ['user'],
  components: {
  },
  data:function()
  {
    return {
      pieces: undefined,
      globalStatistics: undefined,
      statistics: undefined,
    }
  },
  methods: {
    global: function()
    {

    },
    reported: function()
    {

    },
  },
  mounted(){
    if(this.user.permissions.includes('2'))
    {
      /*axios.get("http://localhost:8082/newMenu.php?funcion=getStatistics&id="+ this.user.id)
      .then( data => {
        this.statistics = data.data;
      });*/
      axios({
        method: 'get',
        /*headers: {
          'Access-Control-Allow-Origin': '*',
        },*/
        //headers: [],
        url: 'http://localhost:8082/newMenu.php?funcion=getStatistics&id='+ this.user.id,
      })
      .then(data =>
        this.statistics = data.data
      );
      axios({
        method: 'get',
        /*headers: {
          'Access-Control-Allow-Origin': '*',
        },*/
        //headers: [],
        url: 'http://localhost:8082/newMenu.php?funcion=getReportedPieces',
      })
      .then(data =>
        this.pieces = data.data
      );
    }
  }
}
</script>
<style></style>
