<template>
  <!-- MakeIncidence -->
    <b-form  class="crearP">
        <label>Descripción del problema:</label><br />
        <textarea v-model="description" name="descripcion" rows="10" cols="40" placeholder="resumen del fallo"></textarea><br />
        <p> ¿Que pieza crees que falla?:</p>
        <p> 
            <select v-model="selected" name="pieza" required>
                <option v-for="piece in pieces" :key="piece.id" v-bind:value="piece.name">{{ piece.name }}</option>
            </select>
        </p>
        <button :disabled="!checkForm()" @click="addIncidence()" name="Submit" type="submit">Crear parte</button>
    </b-form><br/>
</template>

<script>
import axios from 'axios';

export default {
  name: 'makeincidence',
  props: ['user'],
  components: {
  },
  data:function()
  {
    return {
      selected: undefined,
      pieces: [],
      checked: false,
      choosen: '--',
      description: undefined,
    }
  },
  methods: {
    addIncidence: function()
    {
      axios({
        method: 'post',
        headers: [],
        url: 'http://localhost:8082/newMenu.php',
        data: {
          funcion: 'addIncidence',
          ownerId: this.user.id,
          issueDesc: this.description,
          pieces: [
            this.getPiece(),
          ]
        }
      })
      .then(
        this.$emit('closeForm')
      );
    },
    checkForm: function()
    {
      return this.selected && this.description? true:false;
    },
    getPiece:function()
    {
      return this.pieces.filter(piece => {
        return piece.name == this.selected;
      })[0];
    },
  },
  mounted(){
    axios.get("http://localhost:8082/newMenu.php?funcion=getPiecesList")
      .then( data => {
        this.pieces = data.data;
    });
  }
}
</script>
<style></style>
