<template>
  <!-- userInfo -->
  <user-info :user="user"/>
  <!-- incidences -->
  <incidences :user="user"/>
  <!-- userInfo -->
  <statistics :user="user"/>
</template>

<script>

import userInfo from './userInfo.vue';
import statistics from './statistics.vue';
import incidences from './incidences.vue';

export default {
  name: 'userPanel',
  props: ['user'],
  components: {
    userInfo,
    statistics,
    incidences,
  },
  data:function()
  {
    return {}
  },
  methods: {},
  mounted(){}
}
</script>
<style></style>
