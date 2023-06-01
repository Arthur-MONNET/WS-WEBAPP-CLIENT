<template>
  <div class="rando-balade-container">
    <button class="btn-retour" @click="goToHome">
      <i class="fas fa-chevron-left"></i> Retour
    </button>

    <div class="search-bar">
      <input type="text" v-model="searchQuery" placeholder="Rechercher une balade" />
      <button class="btn-tri" @click="toggleTri">
        Tri <i :class="{'fas fa-sort-amount-down': isTriAsc, 'fas fa-sort-amount-up': !isTriAsc}"></i>
      </button>
    </div>

    <div class="balades-list">
      <div class="balade-card" v-for="(balade, index) in filteredBalades" :key="index">
        <img class="balade-image" :src="balade.image" :alt="balade.nom" />
        <div class="balade-details">
          <h3 class="balade-nom">{{ balade.nom }}</h3>
          <p class="balade-description">{{ balade.description }}</p>
          <button class="btn-details" @click="goToBalade(balade.id)">Détails</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "RandoBalade",
  data() {
    return {
      balades: [
        {
          id: 1,
          nom: "Balade 1",
          description: "Description de la balade 1",
          image: "/assets/balade1.jpg"
        },
        {
          id: 2,
          nom: "Balade 2",
          description: "Description de la balade 2",
          image: "/assets/balade2.jpg"
        },
        // Ajoutez plus d'objets balade ici...
      ],
      searchQuery: "",
      isTriAsc: true
    };
  },
  computed: {
    filteredBalades() {
      const filtered = this.balades.filter(balade =>
        balade.nom.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
      return this.isTriAsc ? filtered.sort((a, b) => a.nom.localeCompare(b.nom)) : filtered.sort((a, b) => b.nom.localeCompare(a.nom));
    }
  },
  methods: {
    goToHome() {
      this.$router.push("/");
    },
    goToBalade(baladeId) {
      this.$router.push(`/balade/${baladeId}`);
    },
    toggleTri() {
      this.isTriAsc = !this.isTriAsc;
    }
  }
};
</script>

<style scoped>
.rando-balade-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn-retour {
  margin-top: 20px;
  font-size: 16px;
  padding: 8px 16px;
}

.search-bar {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

input[type="text"] {
  padding: 8px;
  margin-right: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
}

.btn-tri {
  padding: 8px 16px;
  background-color: #3490dc;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-tri i {
  margin-left: 4px;
}

.btn-tri:hover {
  background-color: #2779bd;
}

.balades-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 20px;
}

.balade-card {
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

.balade-image {
  width: 100%;
  max-height: 200px;
  object-fit: cover;
  border-radius: 8px;
}

.balade-details {
  margin-top: 10px;
  text-align: center;
}

.balade-nom {
  font-size: 20px;
  margin-bottom: 5px;
}

.balade-description {
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