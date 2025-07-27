<template>
  <div class="container">
    <h1>Voice to Text</h1>
    <VoiceWidget />
  </div>
</template>

<script>
import VoiceWidget from '~/components/VoiceWidget.vue';

export default {
  components: {
    VoiceWidget
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 2rem auto;
  text-align: center;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}
</style>
