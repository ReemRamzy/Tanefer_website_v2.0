<template>
  <div class="whatsapp-container">
    <a :href="whatsappLink" target="_blank" rel="noopener noreferrer">
      <v-btn fab class="whatsapp-button" color="green">
        <v-icon size="28" color="white">mdi-whatsapp</v-icon>
      </v-btn>
    </a>
  </div>
</template>

<script>
export default {
  data () {
    return {
      phoneNumber: '+201080107180'
    }
  },
  computed: {
    whatsappLink () {
      return `https://wa.me/${this.phoneNumber.replace(/\D/g, '')}`
    }
  }
}
</script>

<style scoped>
.whatsapp-container {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 9999;
}

.whatsapp-button {
  width: 55px;
  height: 55px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
}
</style>
