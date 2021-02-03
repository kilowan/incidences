<template>
<div v-if="page == 'Login'" id="Login">
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
</div>
<!-- Menu -->
<div v-if="page == 'Menu'" id="Menu">
  <div class="cabecera">
    <p class="mensaje">Bienvenido {{user.name}} {{user.surname1}} {{user.surname2}}</p>
    <div class="Logo">
      <a @click="logOut()" href="./">
        <img class="cierra" src="./shutdown.png" alt="Cerrar sesión" />
      </a>
    </div>
    <div class="opciones">
      <a @click="add()" v-if="user.permissions.includes('13')" class="link">Crear parte</a>
      <a v-if="incidencesCount >0" class="link" href="veremp.php?id_emp={{user.id}}&dni={{user.dni}}&funcion=Partes">Ver partes</a>
      <a v-if="user.permissions.includes('2')" class="link" href="veremp.php?funcion=Estadisticas&id_emp={{user.id}}&dni={{user.dni}}">Estadísticas</a> 
      <a v-if="user.permissions.includes('16')" class="link" href="veremp.php?funcion=Lista&id_emp={{user.id}}&dni={{user.dni}}">Lista empleados</a> 
      <a class="link" href="veremp.php?funcion=Datos_personales&id_emp={{user.id}}&dni={{user.dni}}">Datos personales</a>
    </div>
  </div>
    <div v-if="check('Crear_parte')" class="cuerpo">
      <make-incidence/>
    </div>
    <div v-else class="cuerpo">
      <p>Not working</p>
    </div>

    <div class="Pie">
      <p>Trabajo realizado por Jose Javier Valero Fuentes y Juan Francisco Navarro Ramiro para el curso de ASIR 2º X</p>
    </div>
</div>
</template>

<script>
import axios from 'axios';
import makeIncidence from './components/makeIncidence.vue'
//import Vue from "vue";
//import VueRouter from 'vue-router'

/*const routes = [
  {
    path: "menu/makeIncidence",
    name: "makeIncidence",
    component: makeIncidence
  },
];*/

/*var router = new VueRouter({
  mode: "history",
  routes
});*/

export default {
  name: 'App',
  components: {
    makeIncidence,
    //Vue,
    //VueRouter,
  },
  data:function()
  {
    return {
      page: 'Login',
      mod: 'Main',
      form: {
        username: undefined,
        pass: undefined,
      },
      user: undefined,
      message: "",
      incidences: undefined,
      incidencesCount: 0,
    }
  },
  methods: {
    check: function(data)
    {
      return this.mod == data? true: false;
    },
    onSubmit: function()
    {
      axios.get("http://localhost:8082/newMenu.php?funcion=checkCredentials&username="+ this.form.username+"&pass="+this.form.pass)
      .then( data => {
        this.response = data.data;
        axios.get("http://localhost:8082/newMenu.php?funcion=getEmployeeByUsername&username="+ this.form.username)
        .then( data => {
          this.user = data.data;
          this.page = 'Menu';
          axios.get("http://localhost:8082/newMenu.php?funcion=getAllincidences")
          .then( data => {
            this.incidences = data.data;
              axios.get("http://localhost:8082/newMenu.php?funcion=getAllincidences")
              .then( data => {
                this.incidences = data.data;
                this.showIncidences();
              });
          });
        });
        console.log(this.response.username);
      });
    },
    add:function()
    {
      //this.$route.go('addIncidence');
      this.mod = 'Crear_parte';
    },
    showIncidences:function()
    {
      let new_array = undefined;
      if(this.user.permissions.includes("7") && this.user.permissions.includes("8") && this.user.permissions.includes("9"))
      {
          new_array = this.incidences.filter(array => {
              return (array.owner.id == this.user.id);
          });
          this.incidencesCount = new_array.length;
      }
      else if (this.user.permissions.includes("3") && this.user.permissions.includes("4") && this.user.permissions.includes("5"))
      {
          new_array = this.incidences.filter(array => {
              return (array.solver.id == this.user.id || array.state == 1);
          });
          this.incidencesCount = new_array.length;
      }
      else if (this.user.permissions.includes("6") && this.user.permissions.includes("7") && this.user.permissions.includes("8") && this.user.permissions.includes("9") && this.user.permissions.includes("10") && this.user.permissions.includes("11") && this.user.permissions.includes("12")) 
      {
          new_array = this.incidences.filter(array => {
              return (array.solver.id == this.user.id || (array.state == 1 || array.state == 2 || array.state == 3 || array.state == 4) || array.owner.id == this.user.id);
          });
          this.incidencesCount = new_array.length;
      }
    },
    logOut:function()
    {
      this.page = 'Login';
      this.mod = 'Main';
      this.form = {
        username: undefined,
        pass: undefined,
      };
      this.user = undefined;
      this.message = "";
      this.incidences = undefined;
      this.incidencesCount = 0;
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
  top: 10%;
	left: 30%;
	width: 40%;
	position: absolute;
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
