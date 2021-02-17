<template>
  <!-- own incidences -->
  <br /><div v-if="!incidence">
    <div v-if="checkPermissions(user.permissions, ['6', '7', '8', '9'])">
      <!-- new -->
      <incidences-view v-if="newOwnIncidences"
      :incidences="newOwnIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes nuevos propios'"
      @linked="linked($event)"/>

      <!-- attended -->
      <incidences-view v-if="attendedOwnIncidences"
      :incidences="attendedOwnIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes atendidos propios'"
      @linked="linked($event)"/>

      <!-- closed -->
      <incidences-view v-if="closedOwnIncidences"
      :incidences="closedOwnIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes cerrados propios'"
      @linked="linked($event)"/>

      <!-- hidden -->
      <incidences-view v-if="hiddenOwnIncidences"
      :incidences="hiddenOwnIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes ocultos propios'"
      @linked="linked($event)"/>
    </div>
    <!-- other incidences -->
    <div v-if="checkPermissions(user.permissions, ['10', '11', '12']) || checkPermissions(user.permissions, ['3', '4', '5'])">
      <!-- new -->
      <incidences-view v-if="newIncidences"
      :incidences="newIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes nuevos'"
      @linked="linked($event)"/>

      <!-- attended -->
      <incidences-view v-if="attendedIncidences"
      :incidences="attendedIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes atendidos'"
      @linked="linked($event)"/>

      <!-- closed -->
      <incidences-view v-if="closedIncidences"
      :incidences="closedIncidences"
      :user="user"
      :admin="admin"
      :title="'Partes cerrados'"
      @linked="linked($event)"/>
    </div>
  </div>
  <div v-else>
    <incidence-view 
      v-if="incidence && checkreload()"
      :user="user" 
      :incidence="incidence"
      @reload="reloading()"
      @stepBack="back()"
      />
  </div>
</template>

<script>

import incidencesView from './incidencesView.vue';
import incidenceView from './incidenceView.vue';

export default {
  name: 'incidences',
  props: ['user', 'incidences', 'admin', 'reload'],
  components: {
    incidencesView,
    incidenceView
  },
  data:function()
  {
    return {
      newOwnIncidences: undefined,
      attendedOwnIncidences: undefined,
      closedOwnIncidences: undefined,
      hiddenOwnIncidences: undefined,
      closedIncidences: undefined,
      attendedIncidences: undefined,
      newIncidences: undefined,
      incidence: undefined,
    }
  },
  methods: {
    linked:function(incidence)
    {
      this.incidence = incidence;
      this.$emit('linked');
    },
    checkPermissions: function(permissions, permissionNumbers)
    {
      let result = true;
      permissionNumbers.forEach(element => {
        if (!permissions.includes(element)) {
          result = false;
        }
      });
      return result;
    },
    reloading: function()
    {
      this.incidence = undefined;
      this.$emit('reload');
    },
    checkreload: function()
    {
      if (!this.reload) {
        return true
      } else {
        this.incidence = undefined;
        return false;
      }
    },
    back: function()
    {
      this.incidence = undefined;
    },
    handle: function()
    {
      this.incidence = undefined;
      if (this.checkPermissions(this.user.permissions, ['6', '7', '8', '9'])) {
        this.newOwnIncidences = this.incidences.filter(data => {
          return data.owner.dni == this.user.dni && data.state == 1;
        });
        this.attendedOwnIncidences = this.incidences.filter(data => {
          return data.owner.dni == this.user.dni && data.state == 2;
        });
        this.closedOwnIncidences = this.incidences.filter(data => {
          return data.owner.dni == this.user.dni && data.state == 3;
        });
        this.hiddenOwnIncidences = this.incidences.filter(data => {
          return data.owner.dni == this.user.dni && data.state == 4;
        });
      }
      if (this.checkPermissions(this.user.permissions, ['10', '11', '12']) || this.checkPermissions(this.user.permissions, ['3', '4', '5'])) {
        this.newIncidences = this.incidences.filter(data => {
          return data.state == 1 && data.owner.id != this.user.id;
        });
        this.attendedIncidences = this.incidences.filter(data => {
          return data.solver.dni == this.user.dni && data.state == 2;
        });
        this.closedIncidences = this.incidences.filter(data => {
          return data.solver.dni == this.user.dni && data.state == 3;
        });
      }
    },
  },
  mounted(){
    this.handle();
  }
}
</script>
<style></style>
