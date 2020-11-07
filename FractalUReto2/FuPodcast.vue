
<template>
  <!--Nuevamente: La forma de aplicar la clase CSS condicional no me funciona, así que empleo esta otra manera-->
  <div
    class="podcast"
    :class="audioActivo ? 'audio--activo' : 'podcast'"
    role="Resultado de búsqueda"
  >
    <img class="podcast__image__enresultadobusqueda" :src="image" :alt="name"/>
    <p class="podcast__name__enresultadobusqueda">{{ name }}</p>
  </div>
</template>
<script>
export default {
  name: "FUPodcast",

  props: {
    image: {
      type: String,
      required: true,
    },
    name: {
      type: String,
      required: true,
    },
    audioActivo: {
      type: Boolean,
      default: false,
      required: false,
    },
  },

};
</script>

<style scoped>
.podcast {
  width: 145px;
  text-align: center;
  padding: 2px;
}
.audio--activo {
  border: 5px solid #2196f3;
  padding: -2px !important;
}
.podcast__image__enresultadobusqueda {
  width: 144px;
  height: 168px;
  object-fit: cover;
}
.podcast__name__enresultadobusqueda {
  margin: 0;
  margin-top: 4px;
  font-weight: bold;
  font-size: 0.875em;
}
</style>