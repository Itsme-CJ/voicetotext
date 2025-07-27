<template>
  <div class="voice-widget">
    <button 
      :class="['mic-btn', { listening: isListening }]"
      @click="toggleRecognition"
      :disabled="isListening && recognition">
      <span v-if="!isListening">🎙️ Start Listening</span>
      <span v-else>🛑 Stop</span>
    </button>
    <div class="transcription">
      <p v-if="interim">{{ interim }}</p>
      <p v-else-if="transcription">{{ transcription }}</p>
      <p v-else class="hint">Click the mic to speak...</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VoiceWidget',
  data() {
    return {
      recognition: null,
      interim: '',
      transcription: '',
      isListening: false,
    };
  },
  mounted() {
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    if (!SpeechRecognition) {
      alert('Speech recognition not supported in this browser');
      return;
    }
    this.recognition = new SpeechRecognition();
    this.recognition.continuous = false;
    this.recognition.interimResults = true;
    this.recognition.lang = 'en-US';

    this.recognition.onstart = () => {
      this.isListening = true;
      this.interim = '';
      this.transcription = '';
    };
    this.recognition.onresult = (e) => {
      const transcript = Array.from(e.results)
        .map(r => r[0].transcript)
        .join('');
      if (e.results[0].isFinal) {
        this.transcription = transcript;
        this.interim = '';
        this.$emit('transcription', transcript);
      } else {
        this.interim = transcript;
      }
    };
    this.recognition.onerror = () => { this.isListening = false; };
    this.recognition.onend = () => { this.isListening = false; };
  },
  methods: {
    toggleRecognition() {
      if (this.isListening) {
        this.recognition.stop();
      } else {
        this.recognition.start();
      }
    }
  }
};
</script>

<style scoped>
.voice-widget {
  text-align: center;
  margin: 2em 0;
}
.mic-btn {
  background: #007BFF;
  color: white;
  font-size: 1rem;
  padding: 0.75em 1.5em;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: background 0.3s;
}
.mic-btn.listening {
  background: #dc3545;
  animation: pulse 1.2s infinite;
}
.transcription {
  margin-top: 1em;
  font-size: 1.2rem;
  min-height: 2em;
}
.hint {
  color: #888;
  font-style: italic;
}
@keyframes pulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(220,53,69,0.7); }
  50% { box-shadow: 0 0 0 15px rgba(220,53,69,0); }
}
</style>
