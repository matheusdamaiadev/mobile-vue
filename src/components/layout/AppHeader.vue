<script setup>
defineProps({
  title: {
    type: String,
    default: 'Meu App',
  },
  showBack: Boolean,
});

defineEmits(['back']);
import { ref, onMounted } from 'vue';

const darkMode = ref(false);

onMounted(() => {
  const saved = localStorage.getItem('darkMode');
  if (saved === 'true') {
    darkMode.value = true;
    document.body.classList.add('dark-mode');
  }
});

function toggleDarkMode() {
  darkMode.value = !darkMode.value;
  document.body.classList.toggle('dark-mode');
  localStorage.setItem('darkMode', darkMode.value);
}

</script>

<template>
  <header class="app-header">
    <button v-if="showBack" @click="$emit('back')" class="btn-back">←</button>
    <h1>{{ title }}</h1>
    <div class="spacer"></div>
    <button @click="toggleDarkMode" class="theme-btn">
  {{ darkMode ? '☀️' : '🌙' }}
</button>

  </header>
</template>

<style scoped>
.app-header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: 56px;
  background: #0b5cff;
  color: white;
  display: flex;
  align-items: center;
  padding: 0 16px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.btn-back {
  background: none;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  padding: 8px;
  margin-right: 8px;
}

.app-header h1 {
  font-size: 18px;
  font-weight: 600;
  flex: 1;
}

.spacer {
  width: 40px; /* Espaço para balancear o botão de voltar */
}
.theme-btn {
  background: transparent;
  border: none;
  font-size: 20px;
  cursor: pointer;
}

</style>