<template>
  <!-- userInfo -->
  <user-info :user="user"/>
  <!-- userInfo -->
  <statistics :user="user"/>
</template>

<script>

import userInfo from './userInfo.vue';
import statistics from './statistics.vue';

export default {
  name: 'userPanel',
  props: ['user'],
  components: {
    userInfo,
    statistics,
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
