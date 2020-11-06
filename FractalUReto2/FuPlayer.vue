<template>
  <div class="fu-player">
    <!--LA ESTRUCTURA DE API DE ALGUNOS "CANALES" ES DISTINTA. EN ALGUNA NO EXISTE LA PORTADA DE CLIP, ASÍ VALIDO SU EXISTENCIA PARA QUE, ENCASO NO EXISTA "PORTADA DE CLIP", UTILIZAR LA "IMAGEN DEL CANAL"-->
    <div
      v-if="
        listaDeClipsDelCanalSeleccionado[clipEnReproduccion].urls.post_image ===
        undefined
      "
    >
      <img
        :src="
          listaDeClipsDelCanalSeleccionado[clipEnReproduccion].channel.urls
            .logo_image.original
        "
        :alt="listaDeClipsDelCanalSeleccionado[clipEnReproduccion].title"
        class="fu-player__image"
      />
    </div>
    <div v-else>
      <img
        :src="
          listaDeClipsDelCanalSeleccionado[clipEnReproduccion].urls.post_image
            .original
        "
        :alt="listaDeClipsDelCanalSeleccionado[clipEnReproduccion].title"
        class="fu-player__image"
      />
    </div>

    <div
      class="fu-player__details"
      role="Información, título y álbum del Podcast"
    >
      <p class="fu-player__name">
        {{ listaDeClipsDelCanalSeleccionado[clipEnReproduccion].title }}
      </p>
      <p class="fu-player__album">
        {{ listaDeClipsDelCanalSeleccionado[clipEnReproduccion].channel.title }}
      </p>
    </div>
    <div class="controlesdereproduccion">
      <div
        class="fu-player__avanzaryretroceder"
        v-on:click="retrocederClip"
        role="Reproducir clip de audio anterior"
      >
        <i class="fas fa-step-backward"></i>
      </div>
      <div
        class="fu-player__play"
        v-on:click="handlePodcast"
        role="Pausar y repoducir el podcast"
      >
        <i v-show="isPaused" class="fas fa-play"></i>
        <i v-show="!isPaused" class="fas fa-pause"></i>
      </div>
      <div
        class="fu-player__avanzaryretroceder"
        v-on:click="avanzarClip"
        role="Reproducir clip de audio siguiente"
      >
        <i class="fas fa-step-forward"></i>
      </div>
    </div>

    <div class="fu-player__volume" role="control de volume">
      <input
        class="fu-player__volumeRange"
        type="range"
        min="0"
        max="100"
        v-model="volumePodcast"
        v-on:input="setVolume"
      />
      <div class="fu-player__volumeIcon" v-on:click="mutearVolume">
        <i v-show="volumePodcast > 0" class="fas fa-volume-up"></i>
        <i v-show="volumePodcast == 0" class="fas fa-volume-mute"></i>
      </div>
    </div>
  </div>
</template>

<script>
//en una version preliminar, hice que los metodos relacionados a la carga del audio sean "async".
export default {
  name: "FuPlayer",
  props: {
    /*name: {type: String, required: true,},
    album: {type: String, required: true,},
    url: {type: String, required: true,},
    image_url: {type: String, required: true,},*/
    listaDeClipsDelCanalSeleccionado: { type: Array, required: true },
  },

  data() {
    return {
      clipEnReproduccion: 0,
      audio: new Audio(this.listaDeClipsDelCanalSeleccionado[0].urls.high_mp3),
      isPaused: true,
      volumePodcast: 100,
      volumePodcastTemp: 1,
    };
  },
 
  methods: {
    handlePodcast() {
      if (this.audio.paused) {
        this.audio.play();
        this.isPaused = false;
      } else {
        this.audio.pause();
        this.isPaused = true;
      }
    },

    retrocederClip() {
      this.audio.pause();
      this.isPaused = true;
      this.clipEnReproduccion--;
      if (this.clipEnReproduccion < 0) {
        this.clipEnReproduccion =
          this.listaDeClipsDelCanalSeleccionado.length - 1;
      }
      this.cambiarDeCancion();
    },
    avanzarClip() {
      this.audio.pause();
      this.isPaused = true;
      this.clipEnReproduccion++;
      if (
        this.clipEnReproduccion >
        this.listaDeClipsDelCanalSeleccionado.length - 1
      ) {
        this.clipEnReproduccion = 0;
      }
      this.cambiarDeCancion();
    },

    cambiarDeCanal() {
      this.audio.pause();
      this.isPaused = true;
      console.log("recibiendo la orden de cambiar de canal");
      /*this.clipEnReproduccion=0;
      this.cambiarDeCancion();*/
    },

   cambiarDeCancion() {
      this.audio = new Audio(
        this.listaDeClipsDelCanalSeleccionado[
          this.clipEnReproduccion
        ].urls.high_mp3
      );
      console.log(
        "Esperando Descarga de: " +
          this.listaDeClipsDelCanalSeleccionado[this.clipEnReproduccion].title +
          " DE " +
          this.listaDeClipsDelCanalSeleccionado[this.clipEnReproduccion].channel
            .title
      );
      this.audio.play();
      this.isPaused = false;
    },

    setVolume() {
      //console.log(this.volumePodcast);
      this.audio.volume = this.volumePodcast / 100;
      //el codigo de ejemplo ocurria un error si reproducimos->bajamos volume a CERO con el range->click en "desmutear". entonces, para emplear el "volumePodcastTemp", valido si, anteriormente, fue puesto en CERO de forma manual.
      if (this.volumePodcast == 0) {
        //El usuario está bajando el volume manualmente!!
        this.audio.muted = true; //muteamos
        this.volumePodcastTemp = 0; //colocamos el temporal en CERO-> será util para un "des-muteado" hacia un nivel de volume bajo
      } else {
        this.audio.muted = false;
        this.volumePodcastTemp = this.volumePodcast;
      }
      console.log(
        this.audio.muted +
          " " +
          this.volumePodcast +
          " " +
          this.volumePodcastTemp
      );
    },
    mutearVolume() {
      //EN ESTE MÉTODO UTILIZO LA VARIABLE "volumePodcastTemp" para regresar el volume a su nivel original. en el ejemplo siempre volvía a 100
      if (this.volumePodcastTemp != 0) {
        //la unica forma que el temporal sea CERO es que el usuario lo haya bajado manualmente con el metodo "setVolume".
        if (this.audio.muted) {
          this.audio.muted = false;
          this.volumePodcast = this.volumePodcastTemp;
        } else {
          this.volumePodcastTemp = this.volumePodcast;
          this.volumePodcast = 0;
          this.audio.muted = true;
          console.log(
            this.audio.muted +
              " " +
              this.volumePodcast +
              " " +
              this.volumePodcastTemp
          ); //probando en consola
          return;
        }
      } else {
        this.audio.muted = false;
        this.volumePodcast = 5;
        this.volumePodcastTemp = 5;
      }
      this.setVolume();
      console.log(
        this.audio.muted +
          " " +
          this.volumePodcast +
          " " +
          this.volumePodcastTemp
      ); //probando en consola
    },
  },
 computed: {
    cargarInformacionClip() {
      //superviso las modificaciones a la listaDeClipsDelCanalSeleccionado.
      return this.listaDeClipsDelCanalSeleccionado;
    },
  },
};
</script>
<style scoped>
/*MEDIA QUERY: para hacer que el reproductor funcione de forma adaptativa a la pantalla, estableciendo que:
    PANTALLA MAYOR A 650px: mostrar nombre del álbum*/
@media screen and (min-width: 651px) {
  .fu-player__name {
    font-size: 14px;
    font-weight: 700;
    /*alineando a la izquierda, ya que se perdía por especifidad del padre*/
    text-align: left !important ;
  }

  .fu-player__album {
    font-weight: 400;
    font-size: 12px;
    /*alineando a la izquierda, ya que se perdía por especifidad del padre*/
    text-align: left !important ;
  }
}

/*PANTALLA MENOR A 650px: ocultamos el nombre del álbum para evitar que se requiera mayor altura para el componente
    y aumentamos un poco el tamaño de texto del nombre del podcast para rellenar el vacío. Además el nombre del álbum aparece en la imagen de portada*/
@media screen and (max-width: 650px) {
  .fu-player__name {
    font-size: 12px;
    font-weight: 600;
    /*alineando a la izquierda, ya que se perdía por especifidad del padre*/
    text-align: left !important ;
  }

  .fu-player__album {
    display: none;
  }
}

/*A PARTIR DE AQUÍ ESTÁN LOS ESTILOS QUE SE APLICARÁN EN LA PÁGINA. De haber empleado un CSS por separado se optaría por otras alternativas que economicen recursos*/

.fu-player {
  display: grid;
  grid-template-columns: 6.25em repeat(3, 1fr);
  background: #2196f3;
  height: 100px;
  width: 100% !important; /*en el video 4 faltó esto, para que el player se adapte a cualquier ancho de pantalla */
}

.fu-player__image {
  width: 100px;
  height: 100px;
  object-fit: cover;
  vertical-align: top;
}

.fu-player__details {
  margin-left: 20px;
  color: #fff;
  align-self: center;
  font-family: Quicksand, Arial;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.controlesdereproduccion {
  display: flex;
  align-items: center;
  justify-self: center;
}

.fu-player__play {
  align-self: center;
  justify-self: center;
  color: #fff;
  font-size: 20px;
  background: #6abdff;
  height: 60px;
  width: 60px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  cursor: pointer;
  margin: 5px;
}
.fu-player__avanzaryretroceder {
  /*align-self: center;
  justify-self: center;*/
  color: #fff;
  font-size: 20px;
  background: none;
  height: 60px;
  width: 60px;
  display: flex;
  justify-content: center;
  align-items: center;
 /* border-radius: 50%;*/
  cursor: pointer;
}

.fu-player__volume {
  display: flex;
  align-items: center;
  justify-self: end;
  margin-right: 32px;
}

.fu-player__volumeRange {
  appearance: none;
  outline: none;
  border-radius: 10px;
  height: 6px;
  cursor: pointer;
}

.fu-player__volumeIcon {
  margin-left: 30px;
  color: #fff;
  /*font-size: 20px; RECIEN ME DOY CUENTA QUE PUSE UN TAMAÑO DE FUENTE DE MÁS*/
  cursor: pointer;
  font-size: 30px;
  justify-content: left;
  align-content: center;
  width: 30px; /*defino un ancho fijo del div, el cual es igual al icono con
  mayor ancho, de esta forma, puedo mantener el icono que se encuentre visible pegado a
  la izquierda, evitando que "salten" cuando se mutea el podcast*/
}
</style>
