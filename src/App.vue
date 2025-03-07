<script setup>
  import {computed, ref} from 'vue';

  const counter = ref(0);

  const doubleCounter = computed(() => counter.value * 2);

  const reset = () => {
    counter.value = 0;
  };
</script>

<template>
  <div>
    <p>Compteur : {{ counter }}</p>
    <p>Double compteur : {{ doubleCounter }}</p>
    <button @click="counter++">Incrémenter</button>
    <button @click="reset">Réinitialiser</button>
    <p v-if="counter >= 5">Le résultat est supérieur à 5</p>
  </div>
</template>