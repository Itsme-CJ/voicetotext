<template>
  <div>
    <vue-speech lang="en-US" @onTranscriptionEnd="handleEnd" />
  </div>
</template>

<script>
export default {
  methods: {
    handleEnd({ transcription }) {
      this.$emit('transcription', transcription)
    }
  }
}
</script>
