<template>
  <div class="recherche-container">
    <button class="btn-retour" @click="goToHome">
      <i class="fas fa-chevron-left"></i> Retour
    </button>

    <div class="recherche-input">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Rechercher une forêt"
      />
      <button class="btn-search" @click="searchForets">
        <i class="fas fa-search"></i>
      </button>
    </div>

    <div class="recherche-results" v-if="searchResults.length > 0">
      <div
        class="result-card"
        v-for="(result, index) in searchResults"
        :key="index"
      >
        <h3 class="result-nom">{{ result.nom }}</h3>
        <p class="result-description">{{ result.description }}</p>
        <button class="btn-details" @click="goToForet(result.id)">
          Détails
        </button>
      </div>
    </div>

    <p v-else>Aucun résultat trouvé.</p>
  </div>
</template>

<script>
export default {
  name: "Recherche",
  data() {
    return {
      searchQuery: "",
      forets: [
        {
          id: 1,
          nom: "Forêt 1",
          description: "Description de la forêt 1",
        },
        {
          id: 2,
          nom: "Forêt 2",
          description: "Description de la forêt 2",
        },
        // Ajoutez plus d'objets forêt ici...
      ],
      searchResults: [],
    };
  },
  mounted() {
    this.searchForets()
  },
  methods: {
    goToHome() {
      this.$router.push("/");
    },
    searchForets() {
      this.searchResults = this.forets.filter((foret) =>
        foret.nom.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
    goToForet(foretId) {
      this.$router.push(`/foret/${foretId}`);
    },
  },
};
</script>

<style scoped>
.recherche-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn-retour {
  margin-top: 20px;
  font-size: 16px;
  padding: 8px 16px;
}

.recherche-input {
  display: flex;
  align-items: center;
  margin-top: 20px;
}

.recherche-input input[type="text"] {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  flex: 1;
}

.btn-search {
  margin-left: 10px;
  padding: 8px 16px;
  background-color: #3490dc;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-search:hover {
  background-color: #2779bd;
}

.recherche-results {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.result-card {
  margin-top: 10px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.result-nom {
  font-size: 20px;
  margin-bottom: 5px;
}

.result-description {
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