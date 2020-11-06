<template>
  <div class="header">
    <img
      src="../assets/imagenes/logo_FractalUp.png"
      class="header__logo"
      alt="FractalUp logo"
    />
  </div>
</template>

<script>
export default {
  name: "FuHeader",
};
</script>

<style scoped>
.header {
  background: #2196f3;
  width: 100%;
  height: 4.375em;
  display: flex;
  justify-content: center;
  align-content: center;
}
.header__logo {
  /*POR ALGUNA RAZÓN NO ME JUSTIFICA CON LA CLASE HEADER (ya verifiqué los estilos computados por si se trata de especifidad), ASÍ QUE:
  si el header tiene de alto 4.375em (70px), y el logo tiene 2.625em(42px), 
  nos queda libres 1.75em (28px)los cuales voy a compartir arriba y abajo, o sea: 0.875em (28px) (anotacion personal: ojala funciones para el responsive)*/
  margin-top: 0.875em;
  width: 9.625em; /*154px*/
  height: 2.625em; /*42px*/
}
</style>