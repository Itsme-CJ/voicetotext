<template>
  <div class="voice-widget-container">
    <h1 class="title">Voice to Text</h1>
    <div class="voice-widget">
      <button
        class="mic-btn"
        :class="{ listening: isListening }"
        @click="toggleRecognition"
        :aria-pressed="isListening.toString()"
        aria-label="Start or stop voice input"
      >
        <span v-if="!isListening">🎙️ Start Listening</span>
        <span v-else>🛑 Stop</span>
      </button>

      <div class="transcript" aria-live="polite">
        <p v-if="interim" class="interim">{{ interim }}</p>
        <p v-else-if="transcription" class="final">{{ transcription }}</p>
        <p v-else class="hint">Tap the mic to start speaking…</p>
      </div>
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
      isListening: false
    };
  },
  mounted() {
    const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
    if (!SR) {
      alert('Speech recognition not supported in this browser');
      return;
    }

    this.recognition = new SR();
    this.recognition.interimResults = true;
    this.recognition.lang = 'en-US';
    this.recognition.continuous = false;

    this.recognition.onstart = () => {
      this.isListening = true;
      this.interim = '';
    };

    this.recognition.onresult = (e) => {
      const text = Array.from(e.results).map(r => r[0].transcript).join('');
      if (e.results[0].isFinal) {
        this.transcription = text;
        this.interim = '';
        this.$emit('transcribed', text);
      } else {
        this.interim = text;
      }
    };

    this.recognition.onend = () => {
      this.isListening = false;
    };

    this.recognition.onerror = (e) => {
      console.error('Speech recognition error:', e.error);
      this.isListening = false;
    };
  },
  methods: {
    toggleRecognition() {
      if (this.isListening) {
        this.recognition.stop();
      } else {
        this.transcription = '';
        this.recognition.start();
      }
    }
  }
};
</script>

<style scoped>
.voice-widget-container {
  max-width: 500px;
  margin: 3rem auto;
  padding: 2rem;
  background-color: #ffffff;
  border-radius: 16px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.1);
  text-align: center;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.title {
  font-size: 2rem;
  font-weight: 600;
  margin-bottom: 2rem;
  color: #222;
}

.mic-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
  font-weight: 500;
  padding: 1rem 1.5rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 32px;
  cursor: pointer;
  transition: background-color 0.25s ease;
}

.mic-btn.listening {
  background-color: #dc3545;
  animation: pulse 1s infinite;
}

.transcript {
  margin-top: 1.5rem;
  font-size: 1.25rem;
  font-weight: 400;
  color: #333;
  min-height: 2em;
}

.interim { color: #999; }
.final { color: #333; }
.hint { color: #bbb; }

@keyframes pulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(220, 53, 69, 0.7); }
  50% { box-shadow: 0 0 0 12px rgba(220, 53, 69, 0); }
}
</style>