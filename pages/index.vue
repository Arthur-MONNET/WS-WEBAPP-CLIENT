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
      :class="[
        'absolute',
        'bottom-28',
        'left-4',
        'z-10',
        'text-gray-700',
        'bg-white',
        'h-12',
        'w-12',
        'rounded-full',
        slideClass,
      ]"
      @click="focusUserMarker"
    >
      <i class="text-2xl fas fa-crosshairs"></i>
    </button>
    <footer
      class="fixed bottom-0 left-0 w-full h-56 flex justify-center items-end"
    ></footer>
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
        @click="openSignalementPopup"
      >
        <i class="text-2xl fas fa-location-dot"></i>
      </button>
    </div>

    <!-- Signalement Popup height = 50% lorsque open -->
    <div class="signalement-popup" :class="{ open: isSignalementPopupOpen }">
      <div
        class="signalement-popup-header"
        @touchmove.prevent
        @touchend="closeSignalementPopup"
      >
        <div class="signalement-popup-header-line"></div>
        <!-- le titre est soit "signalemnt" soit le nom de la catégorie selectionnée si il y en a une ( selectedCategory || "Signalement" )-->
        <h3 class="signalement-popup-header-title">
          {{ selectedCategory ? selectedCategory.title : "Signalement" }}
        </h3>
      </div>
      <button
        v-if="selectedCategory"
        class="return-to-categories"
        @click="selectedCategory = null"
      >
        <i class="fas fa-arrow-left"></i>
      </button>
      <div class="signalement-popup-content">
        <div v-if="!selectedCategory" class="signalement-categories">
          <button
            v-for="category in categories"
            :key="category.id"
            class="signalement-category"
            @click="selectCategory(category)"
          >
            <div
              class="signalement-category-icon"
              :style="{ backgroundColor: category.color }"
            >
              <i :class="category.icon"></i>
            </div>
            <div class="signalement-category-title">{{ category.title }}</div>
          </button>
        </div>
        <div v-if="selectedCategory" class="signalement-list">
          <button
            v-for="signalement in selectedSignalements"
            :key="signalement.id"
            class="signalement-item"
            @click="sendAlert(signalement)"
          >
            <div
              class="signalement-item-icon"
              :style="{ backgroundColor: selectedCategory.color }"
            >
              <i :class="signalement.icon"></i>
            </div>
            <div class="signalement-item-title">{{ signalement.title }}</div>
          </button>
        </div>
      </div>
      <div
        class="signalement-popup-close"
        @touchmove.prevent
        @touchend="closeSignalementPopup"
      ></div>
    </div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl/dist/mapbox-gl.js";
import "mapbox-gl/dist/mapbox-gl.css";

export default {
  name: "Index",

  mounted() {
    // Établir la connexion WebSocket avec le serveur
    this.ws = new WebSocket("ws://172.31.192.1:8080");
    // lorsque la connexion est établie, envoyer un message au serveur
    this.ws.addEventListener("open", () => {
      console.log("Connexion établie avec le serveur");
      this.ws.send(
        JSON.stringify({
          sender: "app",
          type: "connect",
          payload: {
            userId: 1,
          },
        })
      );
    });
    mapboxgl.accessToken = process.env.mapboxToken;
    this.initializeMap();

    // Écouter les messages provenant du serveur
    this.ws.addEventListener("message", this.handleMessage);
  },

  destroyed() {
    this.destroyMap();
  },

  beforeDestroy() {
    // Supprimer l'écouteur d'événement lors de la destruction du composant
    this.ws.removeEventListener("message", this.handleMessage);
  },

  data() {
    return {
      ws: null,
      userLocation: null,
      userMarker: null,
      map: null,
      isUserMarkerCentered: true,
      alerts: [],
      isSignalementPopupOpen: false,
      categories: [
        {
          id: 1,
          title: "Point d'intérêt",
          icon: "fas fa-star",
          // couleur correspondant à la catégorie ( pastel)
          color: "#D9D9FF",
          signalements: [
            { id: 1, title: "Point de vue", icon: "fas fa-eye" },
            { id: 2, title: "Sommet", icon: "fas fa-mountain" },
            { id: 3, title: "Campement", icon: "fas fa-campground" },
            { id: 4, title: "Point Info", icon: "fas fa-info" },
            { id: 5, title: "Point d'intérêt", icon: "fas fa-star" },
          ],
        },
        {
          id: 2,
          title: "Obstacle",
          icon: "fas fa-exclamation-triangle",
          color: "#FFD9B3",
          signalements: [
            { id: 6, title: "Eboulement", icon: "fas fa-mountain" },
            { id: 7, title: "Avalanche", icon: "fas fa-snowflake" },
            { id: 8, title: "Inondation", icon: "fas fa-water" },
            { id: 9, title: "Arbre", icon: "fas fa-tree" },
            { id: 10, title: "Obstacle", icon: "fas fa-exclamation-triangle" },
          ],
        },
        {
          id: 3,
          title: "Zone sensible",
          icon: "fas fa-exclamation",
          color: "#FFB3B3",
          signalements: [
            { id: 11, title: "Travaux", icon: "fas fa-tools" },
            { id: 12, title: "Zone de reproduction", icon: "fas fa-egg" },
            { id: 13, title: "Zone de nidification", icon: "fas fa-feather" },
            { id: 14, title: "Zone sensible", icon: "fas fa-exclamation" },
          ],
        },
        {
          id: 4,
          title: "Danger",
          icon: "fas fa-skull-crossbones",
          color: "#FFB3FF",
          signalements: [
            { id: 15, title: "Abattage", icon: "fas fa-tree" },
            { id: 16, title: "Animal sauvage", icon: "fas fa-paw" },
            { id: 17, title: "Incendie", icon: "fas fa-fire" },
            { id: 18, title: "Nid insecte", icon: "fas fa-bug" },
            { id: 19, title: "Chasse", icon: "fas fa-hiking" },
            { id: 20, title: "Danger", icon: "fas fa-skull-crossbones" },
          ],
        },
        {
          id: 5,
          title: "Animal",
          icon: "fas fa-paw",
          color: "#B3FFB3",
          signalements: [
            { id: 21, title: "Animal blessé", icon: "fas fa-first-aid" },
            { id: 22, title: "Animal mort", icon: "fas fa-skull" },
            { id: 23, title: "Animal en détresse", icon: "fas fa-sad-tear" },
            { id: 24, title: "Chien", icon: "fas fa-dog" },
            { id: 25, title: "Chat", icon: "fas fa-cat" },
            { id: 26, title: "Animal", icon: "fas fa-paw" },
          ],
        },
        {
          id: 6,
          title: "Pollution",
          icon: "fas fa-trash-alt",
          color: "#B3B3FF",
          signalements: [
            { id: 27, title: "Liquide", icon: "fas fa-tint" },
            { id: 28, title: "Encombrant", icon: "fas fa-couch" },
            { id: 29, title: "Déchet", icon: "fas fa-trash-alt" },
            { id: 30, title: "Décharge sauvage", icon: "fas fa-dumpster" },
          ],
        },
      ],
      selectedCategory: null,
      selectedSignalements: [],
    };
  },
  computed: {
    slideClass() {
      return this.isUserMarkerCentered ? "slide-in" : "slide-out";
    },
  },
  methods: {
    handleMessage(event) {
      const message = JSON.parse(event.data);
      const type = message.type;
      const payload = message.payload;

      if (type === "alerts") {
        // Mettre à jour la liste des alertes avec les données provenant du serveur
        this.alerts = payload

        this.alerts.sort((a, b) => {
          return b.latitude - a.latitude;
        });

        // Supprimer les alertes de la carte
        document.querySelectorAll(".alert-marker").forEach((marker) => {
          marker.remove();
        });

        // Ajouter les alertes sur la carte
        // Placer les marqueurs sur la carte pour chaque alerte
        this.alerts.forEach((alert) => {
          const el = document.createElement("div");
          el.className = "alert-marker";
          el.addEventListener("click", () => {
            this.map.flyTo({
              center: [alert.longitude, alert.latitude],
              zoom: 14,
            });
          });

          const icon = document.createElement("i");
          const category = this.categories.find(
            (category) => category.id === alert.category
          );
          const signalement = category.signalements.find(
            (signalement) => signalement.id === alert.type
          );

          icon.className = signalement.icon;
          icon.style.backgroundColor = category.color;
          el.appendChild(icon);

          const svgWidth = 35;
          const svgHeight = 27;
          el.innerHTML +=
            '<svg xmlns="http://www.w3.org/2000/svg" width="' +
            svgWidth +
            '" height="' +
            svgHeight +
            '" viewbox="0 0 ' +
            svgWidth +
            " " +
            svgHeight +
            '">' +
            '<path d="M1.61776 13.049H12.6125C13.0145 13.049 13.3748 13.3017 13.5001 13.6782L17.2537 26.5822C17.4156 27.1393 18.2143 27.1393 18.3762 26.5822L22.1299 13.6782C22.2551 13.3017 22.6154 13.049 23.0174 13.049H33.3909C34.4872 13.049 35.2651 11.9866 34.9153 10.9602L31.4331 0.634932C31.2556 0.103711 30.6344 -0.149004 30.1279 0.0985553L26.0819 2.0945C23.4194 3.40966 20.4801 4.09561 17.4991 4.09561C14.5181 4.09561 11.5841 3.40966 8.91629 2.0945L4.87025 0.0985553C4.36385 -0.154162 3.74258 0.0985538 3.56508 0.634932L0.0828799 10.9602C-0.261686 11.9866 0.510976 13.049 1.60732 13.049H1.61776Z" fill="white"/>' +
            "</svg>";

          const marker = new mapboxgl.Marker(el)
            .setLngLat([alert.longitude, alert.latitude])
            .addTo(this.map);
        });
      }
    },

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
        //variable de precision pour comparer les valeurs de longitude et latitude
        const precision = 0.0001;
        mapCenter.lng = Math.round(mapCenter.lng / precision) * precision;
        mapCenter.lat = Math.round(mapCenter.lat / precision) * precision;
        markerLngLat.lng = Math.round(markerLngLat.lng / precision) * precision;
        markerLngLat.lat = Math.round(markerLngLat.lat / precision) * precision;
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

    openSignalementPopup() {
      this.isSignalementPopupOpen = true;
    },

    closeSignalementPopup() {
      this.isSignalementPopupOpen = false;
      this.selectedCategory = null;
    },

    selectCategory(category) {
      this.selectedCategory = category;
      this.selectedSignalements = category.signalements;
    },

    sendAlert(signalement) {
      // Envoyer l'alerte au serveur via WebSocket
      this.ws.send(
        JSON.stringify({
          sender: "app",
          type: "new-alert",
          payload: {
            category: this.selectedCategory.id,
            type: signalement.id,
            latitude: this.userLocation[1],
            longitude: this.userLocation[0],
            user: 1,
            create_at: new Date().toISOString(),
          },
        })
      );
      console.log(new Date(new Date().toISOString()));
      this.closeSignalementPopup();
    },
  },
};
</script>

<style scoped>
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
  height: calc(100vh + 100px);
  margin: -70px 0 -30px;
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
  border: 2px solid #0b81af;
}

.user-marker::after {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #158de9;
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
  background-color: #26a1ff99;
  transform: translate(-50%, -50%);
}

.alert-marker {
  width: 35px;
  height: 110px;
  padding-bottom: 50px;
  z-index: 20;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
}

.alert-marker i {
  font-size: 20px;
  aspect-ratio: 1/1;
  color: #000000;
  padding: 5px;
  justify-content: center;
  display: flex;
  align-items: center;
  border-radius: 50%;
  border: #fff 2px solid;
  z-index: 20;
}

.alert-marker svg {
  width: 25px;
  height: 20px;
  display: flex;
  z-index: -1;
}

.alert-marker svg path {
  width: 100%;
  height: 100%;
}

.slide-in {
  transform: translateX(-150%);
  transition: transform 0.1s ease-in-out;
}

.slide-out {
  transform: translateX(0%);
  transition: transform 0.3s ease-in-out;
}

/* Signalement Popup */
.signalement-popup {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 80%;
  background-color: white;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  border-radius: 20px 20px 0 0;
  overflow: hidden;
  z-index: 20;
  transition: transform 0.3s ease-in-out;
  transform: translateY(100%);
}

.signalement-popup.open {
  transform: translateY(0%);
}

.signalement-popup-header {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
  width: 100%;
}

.signalement-popup-header-line {
  width: 20px;
  height: 4px;
  background-color: #ccc;
  border-radius: 2px;
  margin-bottom: 8px;
}

.signalement-popup-header-title {
  font-size: 18px;
  font-weight: bold;
}

.signalement-popup-content {
  padding: 32px;
}

.signalement-categories,
.signalement-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 16px;
}

.signalement-category,
.signalement-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 16px;
  cursor: pointer;
}

.signalement-category-icon,
.signalement-item-icon {
  width: 80%;
  font-size: 70px;
  color: #363d38;
  aspect-ratio: 1/1;
  border-radius: 50%;
  background-color: #d3d9d4;
  display: flex;
  align-items: center;
  justify-content: center;
}

.signalement-category-title {
  margin-top: 8px;
  font-size: 12px;
  text-align: center;
}

.signalement-item-title {
  margin-top: 8px;
  font-size: 12px;
  text-align: center;
}

.signalement-popup-close {
  width: 100%;
  height: 20px;
  position: absolute;
  bottom: 0;
  left: 0;
  background-color: transparent;
  cursor: ns-resize;
}

.return-to-categories {
  position: absolute;
  top: 16px;
  left: 16px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: white;
  border: none;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
</style>
