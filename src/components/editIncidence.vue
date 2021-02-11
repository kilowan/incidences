<template>
  <!-- incidenceView -->
		<br />
    <table>
      <tr>
          <th>Editar parte</th>
      </tr>
    </table><br />
		<form action="veremp.php" method="post">
			<input type="hidden" name="id_part" value="'.$incidence->id.'" />
			<input type="hidden" name="funcion" value="Actualizar_parte" />
			<table>
				<tr>
					<th>Nº parte</th>
					<th>Fecha de creación</th>
					<th>Información</th>
					<th>--</th>
				</tr>
				<tr>
					<td>{{incidence.id}}</td>
					<td>{{incidence.initDateTime}}</td>
					<td><input type="text" name="issueDesc" v-model="issueDesc" required /></td>
					<td><a href="#" v-if="issueDesc" @click="updateIncidence()">Guardar</a>
          <a href="#" v-else disabled>Guardar</a></td>
				</tr>
			</table>
		</form>
</template>

<script>

import axios from 'axios';

export default {
  name: 'editIncidence',
  props: ['incidence', 'user'],
  components: {
  },
  data:function()
  {
    return {
      issueDesc: undefined,
    }
  },
  methods: {
    updateIncidence: function()
    {
      if (this.incidence.issueDesc != this.issueDesc) {
        axios({
          method: 'post',
          url: 'http://localhost:8082/newMenu.php',
          data: {
            funcion: 'updateNotes',
            incidenceId: this.incidence.id,
            incidenceDesc: this.issueDesc,
            employeeId: this.user.id,
          },
          headers: [],
        }).then(
          this.$emit('reload')
        );
      } else {
        this.$emit('reloadoff');
      }
    },
  },
  mounted(){
    this.issueDesc = this.incidence.issueDesc;
  }
}
</script>
<style></style>
