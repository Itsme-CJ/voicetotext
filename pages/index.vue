<template>
  <div class="container">
    <h1>Voice to Text</h1>
    <VoiceWidget @transcribed="addTranscription" />
    <div class="transcription-list" v-if="transcriptions.length">
      <h2>Previous Transcriptions</h2>
      <ul>
        <li v-for="(item, index) in transcriptions" :key="index">
          <p>{{ item }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import VoiceWidget from '~/components/VoiceWidget.vue';

export default {
  components: {
    VoiceWidget
  },
  data() {
    return {
      transcriptions: []
    };
  },
  methods: {
    addTranscription(text) {
      this.transcriptions.push(text);
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 2rem auto;
  text-align: center;
}

.transcription-list {
  margin-top: 2rem;
  padding: 1rem;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.transcription-list h2 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: #333;
}

.transcription-list ul {
  list-style-type: none;
  padding: 0;
}

.transcription-list li {
  background-color: #fff;
  margin-bottom: 1rem;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.transcription-list p {
  margin: 0;
  font-size: 1rem;
  color: #555;
}
</style>