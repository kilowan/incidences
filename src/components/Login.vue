<template>
<div>
  <!-- Login -->
  <div class="body">
		<div class="cabecera">
			<div class="nombre">
				<p class="brand">J&J.SA </p>
			</div>
			<div class="mensaje">
				<p class="welcome">Bienvenidos</p>
			</div>
				<div class="login">
					<input class="input" name="username" type="text" id="username" v-model="form.username" placeholder="username" required />
          <input class="input" name="password" type="password" id="password" v-model="form.pass" placeholder="password" required/>
					<button class="btn btn-primary" @click="onSubmit()" v-if="form.username && form.pass" type="submit" variant="primary">LOGIN</button>
				</div>
		</div>
		<div class="cuerpo">
		</div>
		<div class="Pie">
			<p>Trabajo realizado por Jose Javier Valero Fuentes y Juan Francisco Navarro Ramiro para el curso de ASIR 2º X</p>
		</div>
  </div>
</div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'Login',
  components: {
  },
  data:function()
  {
    return {
      form: {
        username: undefined,
        pass: undefined,
      },
      user: undefined,
      incidences: undefined,
    }
  },
  methods: {
    onSubmit: function()
    {
      axios.get("http://localhost:8082/newMenu.php?funcion=checkCredentials&username="+ this.form.username+"&pass="+this.form.pass)
      .then( data => {
        this.response = data.data;
        this.$emit('logedIn', data.data)
      });
    },
  },
  mounted(){}
}
</script>
<style>
.cabecera
{
	border: 2px solid black;
  background-color: #333;
	width: 100%;
	height: 12%;
	left: 0;
	top: 0;
	position: fixed;
	color: white;
	overflow: hidden;
}
</style>
