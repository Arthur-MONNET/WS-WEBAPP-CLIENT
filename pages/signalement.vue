<template>
  <div class="signalement-container">
    <button class="btn-retour" @click="goToHome">
      <i class="fas fa-chevron-left"></i> Retour
    </button>

    <div class="categories-grid">
      <div class="categorie-card" v-for="(categorie, index) in categories" :key="index" @click="openSignalements(categorie)">
        <div class="categorie-icon">
          <i :class="categorie.icon"></i>
        </div>
        <p class="categorie-nom">{{ categorie.nom }}</p>
      </div>
    </div>

    <div v-if="selectedCategorie">
      <h2 class="selected-categorie-nom">{{ selectedCategorie.nom }}</h2>

      <div class="signalements-grid">
        <div class="signalement-card" v-for="(signalement, index) in selectedCategorie.signalements" :key="index">
          <button class="signalement-btn" @click="goToDetails(signalement.id)">
            <div class="signalement-icon">
              <i :class="signalement.icon"></i>
            </div>
            <p class="signalement-nom">{{ signalement.nom }}</p>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Signalement",
  data() {
    return {
      categories: [
        {
          nom: "Catégorie 1",
          icon: "fas fa-exclamation-triangle",
          signalements: [
            { id: 1, nom: "Signalement 1", icon: "fas fa-bug" },
            { id: 2, nom: "Signalement 2", icon: "fas fa-car" },
            // Ajoutez plus d'objets signalement ici...
          ]
        },
        {
          nom: "Catégorie 2",
          icon: "fas fa-flag",
          signalements: [
            { id: 3, nom: "Signalement 3", icon: "fas fa-trash" },
            { id: 4, nom: "Signalement 4", icon: "fas fa-fire" },
            // Ajoutez plus d'objets signalement ici...
          ]
        },
        // Ajoutez plus d'objets catégorie ici...
      ],
      selectedCategorie: null
    };
  },
  methods: {
    goToHome() {
      this.$router.push("/");
    },
    openSignalements(categorie) {
      this.selectedCategorie = categorie;
    },
    goToDetails(signalementId) {
      this.$router.push(`/signalement/${signalementId}`);
    }
  }
};
</script>

<style scoped>
.signalement-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn-retour {
  margin-top: 20px;
  font-size: 16px;
  padding: 8px 16px;
}

.categories-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 20px;
  margin-top: 20px;
}

.categorie-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 50%;
  cursor: pointer;
}

.categorie-icon {
  font-size: 24px;
  margin-bottom: 10px;
}

.categorie-nom {
  text-align: center;
}

.selected-categorie-nom {
  margin-top: 20px;
  font-size: 20px;
}

.signalements-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 20px;
  margin-top: 20px;
}

.signalement-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  cursor: pointer;
}

.signalement-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  border: none;
  background-color: transparent;
  cursor: pointer;
}

.signalement-icon {
  font-size: 24px;
  margin-bottom: 10px;
}

.signalement-nom {
  text-align: center;
}
</style>
