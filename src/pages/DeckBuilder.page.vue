<template>
  <div class="card-list">
    <n-input v-model="searchQuery" placeholder="Rechercher un Pokémon..." class="search-input" />
    
    <n-grid :cols="3" x-gap="12" y-gap="12">
      <n-grid-item v-for="pokemon in filteredPokemonList" :key="pokemon.id">
        <n-card>
          <template #header>
            <div class="card-header">
              <h3>{{ pokemon.name }}</h3>
              <n-tag>{{ pokemon.rarity }}</n-tag>
            </div>
          </template>
          <img :src="pokemon.imageUrl" alt="Image de {{ pokemon.name }}" class="card-image" />
          <template #footer>
            <div class="card-footer">
              <span>Type: {{ pokemon.type }}</span>
              <span>HP: {{ pokemon.hp }}</span>
            </div>
          </template>
        </n-card>
      </n-grid-item>
    </n-grid>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';

interface PokemonCard {
  id: string;
  name: string;
  type: string;
  hp: number;
  imageUrl: string;
  rarity: string;
}

const pokemonList = ref<PokemonCard[]>([]);
const searchQuery = ref<string>('');

const fetchPokemonData = async () => {
  try {
    const response = await axios.get('https://pokemon-api-seyrinian-production.up.railway.app/pokemon-cards');
    
    pokemonList.value = response.data.map((pokemon: any) => ({
      id: String(pokemon.id),
      name: pokemon.name,
      type: pokemon.type?.name || 'Inconnu',
      hp: pokemon.lifePoints,
      imageUrl: pokemon.imageUrl,
      rarity: pokemon.weakness?.name || 'Aucune'
    }));
  } catch (error) {
    console.error('Erreur lors de la récupération des cartes Pokémon :', error);
  }
};

const filteredPokemonList = computed(() => {
  return pokemonList.value.filter(pokemon => 
    pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

onMounted(() => {
  fetchPokemonData();
});
</script>

<style scoped>
.card-list {
  padding: 16px;
}

.search-input {
  margin-bottom: 16px;
  width: 100%;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-image {
  width: 100%;
  height: auto;
  border-radius: 8px;
}

.card-footer {
  display: flex;
  justify-content: space-between;
}
</style>
