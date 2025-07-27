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
        <span v-if="!isListening">🎙️</span>
        <span v-else>🛑</span>
      </button>

      <div class="transcript">
        <p v-if="interim" class="interim">{{ interim }}</p>
        <p v-else-if="transcription" class="final">{{ transcription }}</p>
        <p v-else class="hint">Tap the mic to start speaking…</p>
      </div>
    </div>

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
export default {
  name: 'VoiceWidget',
  data() {
    return {
      recognition: null,
      interim: '',
      transcription: '',
      isListening: false,
      transcriptions: [],
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
      const text = Array.from(e.results)
        .map(r => r[0].transcript)
        .join('');
      if (e.results[0].isFinal) {
        this.transcription = text;
        this.transcriptions.push(text);
        this.interim = '';
        this.$emit('transcribed', text);
      } else {
        this.interim = text;
      }
    };
    this.recognition.onend = () => {
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
  max-width: 600px;
  margin: 2rem auto;
  padding: 2rem;
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.title {
  font-size: 2rem;
  margin-bottom: 1.5rem;
  color: #333;
}

.mic-btn {
  font-size: 2.5rem;
  width: 80px;
  height: 80px;
  border: none;
  border-radius: 50%;
  background: #007bff;
  color: #fff;
  cursor: pointer;
  transition: background 0.25s;
}

.mic-btn.listening {
  background: #dc3545;
  animation: pulse 1s infinite;
}

.transcript {
  margin-top: 1rem;
  min-height: 3em;
  font-size: 1.25rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.interim { color: #999; }
.final { color: #333; }
.hint { color: #bbb; }

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

@keyframes pulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(220,53,69,0.7); }
  50% { box-shadow: 0 0 0 20px rgba(220,53,69,0); }
}
</style>