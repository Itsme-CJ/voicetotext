<template>
  <div>
    <h1>Voice Input Demo</h1>
    <VoiceWidget @transcription="onTranscribe" />
    <div v-if="text" class="output">
      <strong>Output:</strong>
      <p>{{ text }}</p>
    </div>
  </div>
</template>

<script>
import VoiceWidget from '@/components/VoiceWidget.vue';
export default {
  components: { VoiceWidget },
  data() {
    return { text: '' };
  },
  methods: {
    onTranscribe(transcript) {
      this.text = transcript;
    }
  }
};
</script>

<style>
.output {
  margin-top: 1.5em;
  padding: 1em;
  border: 1px solid #ccc;
  border-radius: 8px;
  background: #f9f9f9;
}
</style>

