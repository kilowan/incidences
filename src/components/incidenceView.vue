<template>
  <!-- incidenceView -->
  <br />
    <incidence-module
    v-if="checkIncidence()"
    :user="user" 
    :incidence="incidence"
    @reload="reload()"
    @reloadoff="reloadoff()"
    @stepBack="stepBack()"/>-->
</template>

<script>

import axios from 'axios';
import incidenceModule from './incidenceModule.vue';

export default {
  name: 'incidencesView',
  props: ['incidence', 'user'],
  components: {
    incidenceModule,
  },
  data:function()
  {
    return {
      menu: 'main',
      incidenceData: [],
    }
  },
  methods: {
    back: function()
    {
      this.$emit('stepBack');
    },
    stepBack: function()
    {
      this.load();
      this.$emit('stepBack');
    },
    check: function()
    {
      return Object.keys(this.incidences).length >0;
    },
    load: function()
    {
      axios({
        method: 'get',
        url: 'http://localhost:8082/newMenu.php?funcion=getIncidenceById&id_part=' + this.incidence.id,
      }).then(data => {
        this.incidenceData = data.data;
      });
    },
    checkIncidence: function()
    {
      return this.incidence.owner ? true : false;
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
    change(mod)
    {
      this.menu = mod;
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
    reload:function()
    {
      this.manu='main';
      this.$emit('reload');
    },
    reloadoff:function()
    {
      this.manu='main';
    }
  },
  mounted(){
    this.load();
  }
}
</script>
<style></style>
