<template>
  <div id="app">
    <fu-header class="header" />
    <div v-if="listadePodcasts.length" class="app-listado-resultados">
      <!--la validación sí debería ir, como una buena práctica en caso no haya información para mostrar-->
      <!--intenté usar el V-FOR con el formato con el "ID especial" del ejemplo, sin embargo, me marcaba error, así que empleo la estructura que me proporciona el CLI de Vue-->
      <!--div class="contenedorDeResultados"-->
      <div
        class="app-item-resultados"
        v-for="(podcast, index) in listadePodcasts"
        :key="index"
        @click="cambiandoCanal(), obtenerIDPodcastSeleccionado(podcast)"
      >
        <fu-podcast
          :image="podcast.urls.logo_image.original"
          :name="podcast.title"
          :audioActivo="compararSeleccionadoConElQueSuena(podcast)"
        />
      </div>

      <!--div-->
    </div>
    <div v-if="listaClipsDeAudio.length != 0" class="app-player">
      <fu-player
        ref="reproductor"
        :listaDeClipsDelCanalSeleccionado="cargarPrimerClip"
      ></fu-player>
    </div>
  </div>
</template>

<script>
import FuPlayer from "./components/FuPlayer.vue";
import FuHeader from "./components/FuHeader.vue";
import FuPodcast from "./components/FuPodcast";

export default {
  name: "App",
  components: {
    FuPlayer,
    FuHeader,
    FuPodcast,
  },
  created() {
    fetch("https://api.audioboom.com/channels/recommended")
      .then((estadoDeLaConsulta) => estadoDeLaConsulta.json())
      .then((datosRecibidosEnLaConsulta) => {
        this.listadePodcasts = datosRecibidosEnLaConsulta.body;
      });
  },
  data() {
    return {
      listadePodcasts: [], //en realidad hace referencia a una lista de canales.
      listaClipsDeAudio: [], //es la lista de clips de audio que dispone el canal seleccionado (se obtuvo a través de la función obtenerAudioPorID() ).
      canalSeleccionadoPorUsuario: "nombre canal",
    };
  },
  computed: {
    cargarPrimerClip() {
      //return this.listaClipsDeAudio[0];//en el video de entrenamiento manda sólo el primer audio
      console.log(this.listaClipsDeAudio);
      return this.listaClipsDeAudio; //necesito mandar toda la lista!
    },
  },
  methods: {
    cambiandoCanal() {
      try {
        //esto fue un intento de hacer que el componente hijo detenga la reproduccion e inicie la reproducción de un nuevo canal
        console.log("enviando la orden de cambiar el canal...");
        this.$refs.reproductor.cambiarDeCanal(); //esta llamada a procedimiento genera un error en el primer intento, hasta que cachea la direccion de destino
      } catch (error) {
        console.log("El destino tardó en alcanzarse, reintente. Ref: " + error);
      }
    },

    async obtenerIDPodcastSeleccionado(podcast) {
      //"podcast" hacer referencia a un canal
      const { id } = podcast;
      const audioClips = await this.obtenerAudioPorID(id);
      this.listaClipsDeAudio.length = 0;
      this.listaClipsDeAudio = audioClips.audio_clips; //recuperamos la lista de clips que tiene el canal
      this.canalEnReproduccion = podcast; //señalamos cuál es el canal que está seleccionado
    },
    obtenerAudioPorID(id) {
      //obtiene la lista de clips de audio que tiene el canal seleccionado
      //No me funcionaba con la sintaxis del ejemplo (igual como pasó con el :key ID), así que, como recibe un String, lo estoy concatenando al ID (Y)
      return fetch("https://api.audioboom.com/channels/" + id + "/audio_clips")
        .then((respuesta) => respuesta.json())
        .then((data) => data.body);
    },
    compararSeleccionadoConElQueSuena(podcast) {
      if (this.canalEnReproduccion) {
        return podcast.id === this.canalEnReproduccion.id;
      }
      return false;
    },
  },
};
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;
  color: #2c3e50;*/
  margin-top: 0px;
}
.app-player {
  position: fixed;
  bottom: 0;
  width: 100%;
}
.app-listado-resultados {
  display: flex;
  flex-wrap: wrap;
  margin-top: 5.375em; /*para que al hacerlo responsive, no se oculte tras el header */
  margin-bottom: 100px; /*para que los resultados no se vean parcialmente cubiertos por el app-player */
  justify-content: start;
}
.header {
  position: fixed;
  top: 0px;
}
.app-item-resultados {
  display: inline-flex;
  cursor: pointer;
  margin-bottom: 32px;
  margin-left: 16px;
}
</style>