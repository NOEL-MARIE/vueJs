<template>
    <label>
      <input v-model="model" type="checkbox" @change="OnChange" />
      {{ label }}
    </label>
  </template>
  
  <script setup>
  import { ref, defineProps, defineEmits } from 'vue'
  
  // Définir les propriétés d'entrée pour le label
  const props = defineProps({
    label: String,
  })
  
  // Définir les événements à émettre pour signaler si la case est cochée ou décochée
  const emit = defineEmits(['check', 'uncheck'])
  
  // Créer une ref pour le modèle lié à l'état de la case
  const model = ref('')
  
  const OnChange = (event) => {
  
  
    // Émettre l'événement 'check' ou 'uncheck' selon l'état
    if (checked) {
      emit('check', event.currentTarget)
    } else {
      emit('uncheck', event.currentTarget)
    }
  }
  </script>
  