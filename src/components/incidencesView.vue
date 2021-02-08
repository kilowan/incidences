<template>
  <!-- incidenceView -->
  <div v-if="check()">
    <table>
        <tr>
            <th colspan="10">{{title}}</th>
        </tr>
    </table>
    <table>
      <tr>
        <th>Nº parte</th>
        <th>Fecha de creación</th>
        <th>Información</th>
      </tr>
      <tr v-for="(incidence, index) in incidences" v-bind:key="index">
        <td><a href="veremp.php?id_part={{incidence.id}}&funcion=Ver_parte&state={{incidence.state}}">{{incidence.id}}</a></td>
            <td v-if="incidence.initDateTime">{{incidence.initDateTime}}</td>
            <td v-if="incidence.issueDesc">{{incidence.issueDesc}}</td>
        </tr>
    </table><br />
  </div>
</template>

<script>

export default {
  name: 'incidencesView',
  props: ['incidences', 'title'],
  components: {
  },
  data:function()
  {
    return {}
  },
  methods: {
    check: function()
    {
      return Object.keys(this.incidences).length >0;
    }
  },
  mounted(){}
}
</script>
<style></style>
