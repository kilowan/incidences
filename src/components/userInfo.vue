<template>
  <!-- userInfo -->
  <br /><table>
    <tr>
      <th>Datos personales</th>
    </tr>
  </table><br />
  <table>
    <tr>
      <td>Id Empleado</td>
      <td>{{ user.id }}</td>
    </tr>
    <tr>
      <td>DNI</td>
      <td>{{ user.dni }}</td>
    </tr>
    <tr>
      <td>Nombre</td>
      <td>{{ user.name }}</td>
    </tr>
    <tr>
      <td>Primer apellido</td>
      <td>{{ user.surname1 }}</td>
    </tr>
    <tr>
      <td>Segundo apellido</td>
      <td>{{ user.surname2 }}</td>
    </tr>
    <tr>
      <td>Tipo</td>
      <td>{{ user.tipo }}</td>
    </tr>
  </table><br />
</template>

<script>

export default {
  name: 'userInfo',
  props: ['user'],
  components: {
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
