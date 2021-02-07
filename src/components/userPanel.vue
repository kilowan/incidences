<template>
  <!-- userInfo -->
  <user-info :user="user"/>
</template>

<script>

import userInfo from './userInfo.vue';

export default {
  name: 'userPanel',
  props: ['user'],
  components: {
    userInfo,
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
