<template>
  <div class="notification-container">
    <button class="btn-retour" @click="goToHome">
      <i class="fas fa-chevron-left"></i> Retour
    </button>

    <h2 class="page-title">Notifications</h2>

    <div class="notification-list">
      <div class="notification-card" v-for="(notification, index) in notifications" :key="index">
        <div class="notification-content">
          <h3 class="notification-title">{{ notification.title }}</h3>
          <p class="notification-message">{{ notification.message }}</p>
        </div>
        <div class="notification-actions">
          <button class="btn-join" v-if="notification.joinable" @click="joinEvent(notification.eventId)">
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
      notifications: [
        {
          id: 1,
          title: "Événement 1",
          message: "Nouvel événement à venir",
          joinable: true,
          eventId: 1
        },
        {
          id: 2,
          title: "Alerte 1",
          message: "Alerte importante",
          joinable: false
        },
        // Ajoutez plus d'objets notification ici...
      ]
    };
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
    }
  }
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
  width: 400px;
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
