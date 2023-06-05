<template>
  <div class="notification-container">
    <button class="btn-retour" @click="goToHome">
      <i class="fas fa-chevron-left"></i> Retour
    </button>

    <h2 class="page-title">Notifications</h2>

    <div class="notification-list">
      <div
        class="notification-card"
        v-for="(notification, index) in notifications"
        :key="index"
      >
        <div class="notification-content">
          <h3 class="notification-title">{{ notification.type }}</h3>
          <p class="notification-delay">il y a {{ notification.delay }}</p>
        </div>
        <div class="notification-actions">
          <button
            class="btn-join"
            v-if="notification.joinable"
            @click="joinEvent(notification.eventId)"
          >
            Rejoindre
          </button>
          <button class="btn-details" @click="viewDetails(notification.id)">
            Détails
          </button>
        </div>
      </div>
      <div v-if="notifications.length === 0" class="empty-notification">
        Aucune notification pour le moment.
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Notification",
  data() {
    return {
      notifications: [],
    };
  },
  mounted() {
    // Établir la connexion WebSocket avec le serveur
    const ws = new WebSocket("ws://172.31.192.1:8080");
    // lorsque la connexion est établie, envoyer un message au serveur
    ws.addEventListener("open", () => {
      console.log("Connexion établie avec le serveur");
      ws.send(
        JSON.stringify({
          sender: "app",
          type: "connect",
          payload: {
            userId: 1,
          },
        })
      );
    });

    // Écouter les messages provenant du serveur
    ws.addEventListener("message", (event) => {
      const message = JSON.parse(event.data);
      console.log("Message reçu du serveur :", message);
      const type = message.type;
      const payload = message.payload;

      if (type === "alerts") {
        // Mettre à jour la liste des alertes avec les données provenant du serveur
        this.notifications = payload;
        this.notifications.sort((a, b) => {
          const dateA = new Date(a.created_at);
          const dateB = new Date(b.created_at);
          return dateB - dateA;
        });

        // Ajouter un délai à chaque notification
        this.notifications.forEach((notification) => {
          // created_at avec le décalahe horaire
          const created_at = new Date(notification.created_at);
          console.log("created_at :", created_at);
          const now = new Date();
          console.log("now :", now);
          const diff = now - created_at;
          const minutes = Math.floor(diff / 1000 / 60);
          const hours = Math.floor(minutes / 60);
          const days = Math.floor(hours / 24);
          const months = Math.floor(days / 30);
          const years = Math.floor(months / 12);

          if (years > 0) {
            notification.delay = `${years} an${years > 1 ? "s" : ""}`;
          } else if (months > 0) {
            notification.delay = `${months} mois`;
          } else if (days > 0) {
            notification.delay = `${days} jour${days > 1 ? "s" : ""}`;
          } else if (hours > 0) {
            notification.delay = `${hours} heure${hours > 1 ? "s" : ""}`;
          } else if (minutes > 0) {
            notification.delay = `${minutes} minute${minutes > 1 ? "s" : ""}`;
          } else {
            notification.delay = "quelques secondes";
          }
        });
      }
    });
  },
  methods: {
    goToHome() {
      this.$router.push("/");
    },
    joinEvent(eventId) {
      // Ajoutez la logique pour rejoindre l'événement
      console.log("Rejoindre l'événement :", eventId);
    },
    viewDetails(notificationId) {
      // Ajoutez la logique pour afficher les détails de la notification
      console.log("Voir les détails de la notification :", notificationId);
    },
  },
};
</script>

<style scoped>
.notification-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn-retour {
  margin-top: 20px;
  font-size: 16px;
  padding: 8px 16px;
}

.page-title {
  font-size: 24px;
  margin-top: 20px;
}

.notification-list {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.notification-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  margin-bottom: 10px;
}

.notification-content {
  flex-grow: 1;
}

.notification-title {
  font-size: 20px;
  margin-bottom: 5px;
}

.notification-message {
  margin-bottom: 10px;
}

.notification-actions {
  display: flex;
  align-items: center;
}

.btn-join,
.btn-details {
  padding: 8px 16px;
  margin-left: 10px;
  background-color: #3490dc;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-join:hover,
.btn-details:hover {
  background-color: #2779bd;
}

.empty-notification {
  margin-top: 10px;
  font-style: italic;
  color: #999;
}
</style>
