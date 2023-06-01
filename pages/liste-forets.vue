<template>
  <div class="forets-container">
    <button class="btn-retour" @click="goToHome">
      <i class="fas fa-chevron-left"></i> Retour
    </button>

    <div class="forets-list">
      <div class="foret-card" v-for="(foret, index) in forets" :key="index">
        <img class="foret-image" :src="foret.image" :alt="foret.nom" />
        <div class="foret-details">
          <h3 class="foret-nom">{{ foret.nom }}</h3>
          <p class="foret-description">{{ foret.description }}</p>
          <button class="btn-details" @click="goToForet(foret.id)">Détails</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ListeForets",
  data() {
    return {
      forets: [
        {
          id: 1,
          nom: "Forêt 1",
          description: "Description de la forêt 1",
          image: "/assets/foret1.jpg"
        },
        {
          id: 2,
          nom: "Forêt 2",
          description: "Description de la forêt 2",
          image: "/assets/foret2.jpg"
        },
        // Ajoutez plus d'objets foret ici...
      ]
    };
  },
  methods: {
    goToHome() {
      this.$router.push("/");
    },
    goToForet(foretId) {
      this.$router.push(`/foret/${foretId}`);
    }
  }
};
</script>

<style scoped>
.forets-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn-retour {
  margin-top: 20px;
  font-size: 16px;
  padding: 8px 16px;
}

.forets-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 20px;
}

.foret-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 300px;
  margin: 10px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.foret-image {
  width: 100%;
  max-height: 200px;
  object-fit: cover;
  border-radius: 8px;
}

.foret-details {
  margin-top: 10px;
  text-align: center;
}

.foret-nom {
  font-size: 20px;
  margin-bottom: 5px;
}

.foret-description {
  margin-bottom: 10px;
}

.btn-details {
  padding: 8px 16px;
  background-color: #3490dc;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-details:hover {
  background-color: #2779bd;
}
</style>