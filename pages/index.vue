<template>
  <div id="page-container">
    <div id="map-container"></div>
    <button
      class="absolute top-4 left-4 text-gray-700 bg-white h-12 w-12 rounded-full"
      @click="goToMenu"
    >
      <i class="text-2xl fas fa-bars"></i>
    </button>

    <button
      class="absolute top-4 right-4 text-gray-700 bg-white h-12 w-12 rounded-full"
      @click="goToRecherche"
    >
      <i class="text-2xl fas fa-search"></i>
    </button>
    <button
      :class="['absolute', 'bottom-28', 'left-4', 'z-10', 'text-gray-700', 'bg-white', 'h-12', 'w-12', 'rounded-full', slideClass]"
      @click="focusUserMarker"
    >
      <i class="text-2xl fas fa-crosshairs"></i>
    </button>
    <footer
      class="fixed bottom-0 left-0 w-full h-56 flex justify-center items-end"
    >
      
    </footer>
    <div
        class="h-14 flex bg-gray-200 justify-center items-center px-3 mb-6 rounded-full z-10 absolute bottom-0 mx-auto w-min mx-auto"
      >
        <button
          class="text-xl text-gray-700 bg-white h-9 w-9 rounded-full mr-3"
          @click="goToListeForet"
        >
          <i class="fas fa-tree"></i>
        </button>
        <button
          class="text-xl text-gray-700 bg-white h-9 w-9 rounded-full mr-3"
          @click="goToListeRando"
        >
          <i class="fas fa-hiking"></i>
        </button>
        <button
          class="text-xl text-gray-700 bg-white h-9 w-9 rounded-full mr-3"
          @click="goToNotification"
        >
          <i class="fas fa-bell"></i>
        </button>
        <button
          id="signalement"
          class="bg-red-500 text-white border-white border-solid border-4 rounded-full h-16 w-16 flex items-center justify-center ml-2"
          @click="goToSignalement"
        >
          <i class="text-2xl fas fa-location-dot"></i>
        </button>
      </div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl/dist/mapbox-gl.js";
import "mapbox-gl/dist/mapbox-gl.css";

export default {
  name: "Index",

  mounted() {
    mapboxgl.accessToken = process.env.mapboxToken;
    this.initializeMap();
  },

  destroyed() {
    this.destroyMap();
  },

  data() {
    return {
      userLocation: null,
      userMarker: null,
      map: null,
      isUserMarkerCentered: true,
    };
  },
  computed: {
    slideClass() {
      return this.isUserMarkerCentered ? "slide-in" : "slide-out";
    },
  },
  methods: {
    async initializeMap() {
      this.map = new mapboxgl.Map({
        container: "map-container",
        style: "mapbox://styles/arthyork/clhunucul007g01pnao3n6s31",
        zoom: 12,
      });

      await this.waitForGeolocation(() => {
        navigator.geolocation.getCurrentPosition((position) => {
          const { latitude, longitude } = position.coords;
          this.userLocation = [longitude, latitude];
          this.map.setCenter(this.userLocation);
          this.map.flyTo({
            center: this.userLocation,
            zoom: 14,
          });
          if (this.userMarker) {
            this.userMarker.remove();
          }
          const el = document.createElement("div");
          el.className = "user-marker";
          this.userMarker = new mapboxgl.Marker(el)
            .setLngLat(this.userLocation)
            .addTo(this.map);
          this.userMarker
            .getElement()
            .addEventListener("click", this.focusUserMarker);
        });
      });

      this.map.on("move", () => {
        const mapCenter = this.map.getCenter();
        const markerLngLat = this.userMarker ? this.userMarker.getLngLat() : {};
        // arondir à 6 décimales pour éviter les erreurs d'arrondi
        mapCenter.lng = Math.round(mapCenter.lng * 1000000) / 1000000;
        mapCenter.lat = Math.round(mapCenter.lat * 1000000) / 1000000;
        markerLngLat.lng = Math.round(markerLngLat.lng * 1000000) / 1000000;
        markerLngLat.lat = Math.round(markerLngLat.lat * 1000000) / 1000000;
        this.isUserMarkerCentered =
          mapCenter.lng === markerLngLat.lng &&
          mapCenter.lat === markerLngLat.lat;
      });
    },

    destroyMap() {
      if (this.map) {
        this.map.remove();
        this.map = null;
      }
    },

    waitForGeolocation(callback) {
      return new Promise((resolve) => {
        if (navigator.geolocation) {
          callback();
          resolve();
        } else {
          setTimeout(() => {
            resolve(this.waitForGeolocation(callback));
          }, 500);
        }
      });
    },

    focusUserMarker() {
      if (this.userMarker) {
        const { lng, lat } = this.userMarker.getLngLat();
        this.map.flyTo({
          center: [lng, lat],
          zoom: 14,
        });
      }
    },

    goToMenu() {
      if (this.map) {
        this.destroyMap();
      }
      this.$router.push("/menu");
    },

    goToRecherche() {
      if (this.map) {
        this.destroyMap();
      }
      this.$router.push("/recherche");
    },

    goToListeForet() {
      this.$router.push("/liste-forets");
    },

    goToListeRando() {
      this.$router.push("/rando-balade");
    },

    goToSignalement() {
      this.$router.push("/signalement");
    },

    goToNotification() {
      this.$router.push("/notification");
    },
  },
};
</script>

<style>
#page-container {
  width: 100%;
  height: 100vh;
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
}

#map-container {
  width: 100%;
  height: calc(100vh + 60px);
  margin: -30px 0;
}

/*footer gradient background and event clck not working*/

footer {
  background: linear-gradient(
    180deg,
    rgba(255, 255, 255, 0) 0%,
    rgba(255, 255, 255, 0.8) 100%
  );
  pointer-events: none;
}

#signalement {
  margin-top: -48px;
  margin-right: -44px;
}

.user-marker {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background-color: transparent;
  border: 2px solid #f0f7f1;
}

.user-marker::after {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #ffb526;
  transform: translate(-50%, -50%);
}

.user-marker::before {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #ffb526;
  transform: translate(-50%, -50%);
  filter: blur(10px);
}

.slide-in {
  transform: translateX(-150%);
  transition: transform 0.1s ease-in-out;
}

.slide-out {
  transform: translateX(0%);
  transition: transform 0.3s ease-in-out;
}
</style>
