<template>
  <!-- incidenceView -->
  <div v-if="check()">
    <div v-if="checkMenu('incidences')">
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
        <td><a href="#" @click="detail(incidence)">{{incidence.id}}</a></td>
            <td v-if="incidence.initDateTime">{{incidence.initDateTime}}</td>
            <td v-if="incidence.issueDesc">{{incidence.issueDesc}}</td>
        </tr>
    </table><br />
    </div>
  </div>
</template>

<script>

export default {
  name: 'incidencesView',
  props: ['incidences', 'user', 'title'],
  components: {
  },
  data:function()
  {
    return {
      menu: 'incidences',
      incidenceData: undefined,
    }
  },
  methods: {
    check: function()
    {
      return Object.keys(this.incidences).length >0;
    },
    checkMenu: function(data)
    {
      return this.menu == data ? true : false;
    },
    detail: function(incidence)
    {
      this.menu = 'detail';
      this.incidenceData = incidence;
      this.$emit('linked', incidence);
    }
  },
  mounted(){}
}
</script>
<style></style>
