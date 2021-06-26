<template v-if="data">
  <tr>
    <cell
      v-for="(info, index) in data" v-bind:key="index"
      :header="header" 
      :link="info.arrayData.own.length >0 || info.arrayData.other.length >0" 
      :text="info.inName" 
      @select-tab="$emit('selectTab', info.outName)"
  />
  </tr>
</template>

<script>

import cell from '../custom/cell.vue';

export default {
  name: 'row',
  props: ['header', 'data'],
  components: {
    cell
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
