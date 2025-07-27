<template>
  <div>
    <h1>Voice to Text Test</h1>
    <VoiceWidget @transcription="onTranscribe" />
    <p>Transcript: {{ transcript }}</p>
  </div>
</template>

<script>
import VoiceWidget from '@/components/VoiceWidget'

export default {
  components: { VoiceWidget },
  data() {
    return { transcript: '' }
  },
  methods: {
    onTranscribe(text) {
      this.transcript = text
    }
  }
}
</script>
