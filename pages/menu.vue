<template>
  <div>
    <button class="text-gray-700 bg-white h-12 w-12 rounded-full" @click="goToHome">
      <i class="text-2xl fas fa-arrow-left"></i>
      <!-- Utilisez la classe correspondante à l'icône de flèche gauche de Font Awesome -->
    </button>

    <div class="flex flex-col items-center mt-10">
      <h1 class="text-2xl font-bold mb-6">Menu</h1>

      <button class="mb-4" @click="goToProfile">
        Profile
      </button>

      <button class="mb-4" @click="goToParametres">
        Paramètres
      </button>

      <button class="mb-4" @click="goToAideFaq">
        Aide/FAQ
      </button>

      <button class="mb-4" @click="deconnecter">
        Déconnexion
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Menu",
  methods: {
    goToHome() {
      this.$router.push("/");
    },
    goToProfile() {
      this.$router.push("/profile");
    },
    goToParametres() {
      this.$router.push("/parametres");
    },
    goToAideFaq() {
      this.$router.push("/aide-faq");
    },
    deconnecter() {
      // Ajoutez ici la logique pour déconnecter l'utilisateur
    },
  },
};
</script>

<style scoped>
button {
  background-color: #f8f8f8;
  border: 1px solid #ccc;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 16px;
}
</style>