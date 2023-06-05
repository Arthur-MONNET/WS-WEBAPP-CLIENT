<template>
  <button class="return-btn" @click="goBack">
    <i class="fas fa-chevron-left"></i> Retour
  </button>
</template>

<script>
export default {
  name: 'return-btn',
  methods: {
    goBack() {
      this.$router.go(-1); // Retourne à la page précédente
    }
  }
};
</script>

<style scoped>
.return-btn {
  margin-top: 20px;
  font-size: 16px;
  padding: 8px 16px;
}
</style>