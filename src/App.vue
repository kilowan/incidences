<template>
  <!-- Login -->
  <div class="body">
		<div class="cabecera">
			<div class="nombre">
				<p>J&J.SA </p>
			</div>
			<div class="mensaje">
				<p>Bienvenidos</p>
			</div>
      <b-form>
				<div class="login">
					<input name="username" type="text" id="username" v-model="form.username" placeholder="username" required />
          <input name="password" type="text" id="password" v-model="form.pass" placeholder="password" required/>
					<button @click="onSubmit()" v-if="form.username && form.pass" type="submit" variant="primary">LOGIN</button>
				</div>
			</b-form>
		</div>
		<div class="cuerpo">
		</div>
		<div class="Pie">
			<p>Trabajo realizado por Jose Javier Valero Fuentes y Juan Francisco Navarro Ramiro para el curso de ASIR 2º X</p>
		</div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  components: {
  },
  data:function()
  {
    return {
      Id: 3,
      response: {
        name: "",
        surname1: "",
        surname2: "",
        dni: "",
        tipo: "",
        id: undefined,
        permissions: [],
        borrado: 0,
      },
      form: {
        username: undefined,
        pass: undefined,
      },
      user: undefined,
      message: "",
    }
  },
  methods: {
    messageUser: function() {
      return "Welcome to Your Vue.js App " + this.response.name + " " + this.response.surname1;
    },
    onSubmit: function()
    {
      axios.get("http://localhost:8082/newMenu.php?funcion=checkCredentials&username="+ this.form.username+"&pass="+this.form.pass)
      .then( data => {
        this.response = data.data;
        axios.get("http://localhost:8082/newMenu.php?funcion=getEmployeeByUsername&username="+ this.form.username)
        .then( data => {
          this.user = data.data;
        });
        console.log(this.response.username);
      });
    }
  },
  mounted:function()
  {
    /*axios.get("http://localhost:8082/newMenu.php?funcion=getEmployeeById&id="+ this.Id)
    .then( data => {
      this.response = data.data;
      console.log(this.response.name);
    });*/
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
body
{
	background:url("fondo.gif");
	font-size: 100%;
	font-family: sans-serif;
}
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
.mensaje
{
	text-align: center;
  font-size: 1.4em;
  font-weight: bold;
	color: white;
	top: 5%;
	width: 40%;
	left: 30%;
	position: absolute;
}
.Logo
{
	text-align: right;
	right: 5%;
	top: 10%;
	position: absolute;
}
img.cierra
{
	width: 40px; height: 40px;
}
.login
{
	font-size: 0.9em;
	right: 1%;
	top: 5%;
	position: fixed;
}
.opciones
{
	text-align: center;
	font-size: 0.9em;
	bottom: 0;
	width: 60%;
	left: 20%;
	position: absolute;
}
.link
{
	color: white;
}
.cuerpo
{
	top: 12%;
	left: 0%;
	width: 100%;
	bottom: 10%;
	position: fixed;
	overflow: auto;
}
.tabla_tecnico
{
	left: 10%;
	width: 80%;
	position: relative;
}
.tabla_ranking, .tabla_tiempo
{
	left: 30%;
	width: 40%;
}
.crearP, .nuevoemp
{
	top: 1%;
	text-align: center;
	border: 2px solid black;
    background-color: #d7dee3;
	left: 30%;
	width: 40%;
	position: relative;
}
.mod_parte
{
	text-align: center;
	border: 2px solid black;
    background-color: #d7dee3;
	width: 30%;
	left: 35%;
	position: absolute;
}
table
{
	box-shadow: 5px 5px 10px #999;
	border: 1px solid white;
    background: white;
	top: 1%;
	left: 10%;
	width: 80%;
	position: relative;
	border-spacing: 0px;
}
th
{
	border: 1px solid white;
    color: black;
	background: #d7dee3 url("tabla.gif") repeat-x top left;
}
td
{
	font-size: 100%;
	text-align: center;
	border: 1px dashed gray;
	color: black;
}
.Pie
{
	bottom: 0;
	left: 0;
	right: 0;
	height: 10%;
	position: fixed;
	background-color: #333;
	color: white;
	text-align: center;
}
.respuesta
{
	text-align: center;
	left: 35%;
	width: 30%;
	position: relative;
	background: #d7dee3;
}
</style>
