<template>
  <div class="card-list">
    <n-grid :cols="3" x-gap="12" y-gap="12">
      <n-grid-item v-for="pokemon in pokemonList" :key="pokemon.id">
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
import { ref, onMounted } from 'vue';
import axios from 'axios';

interface PokemonApiResponse {
  id: number;
  name: string;
  pokedexId: number;
  typeId: number;
  lifePoints: number;  // L'API renvoie 'lifePoints', donc il faut l'ajouter
  imageUrl: string;
  height: number;
  weight: number;
  attackId: number;
  weaknessId: number;
  type: {
    id: number;
    name: string;
  };
  attack: {
    id: number;
    name: string;
    damages: number;
    typeId: number;
    type: {
      id: number;
      name: string;
    };
  };
  weakness: {
    id: number;
    name: string;
  };
}

interface PokemonCard {
  id: string;
  name: string;
  type: string;
  hp: number;
  imageUrl: string;
  rarity: string;
}


const pokemonList = ref<PokemonCard[]>([]);

const fetchPokemonData = async () => {
  try {
    const response = await axios.get<PokemonApiResponse[]>('https://pokemon-api-seyrinian-production.up.railway.app/pokemon-cards');
    
    console.log("Données Pokémon récupérées :", response.data);

    // Mapper les données de l'API au format attendu par l'affichage
    pokemonList.value = response.data.map(pokemon => ({
      id: String(pokemon.id), // Convertit en string pour éviter les erreurs dans v-for
      name: pokemon.name,
      type: pokemon.type.name, // Accès correct à l'objet type
      hp: pokemon.lifePoints,  // Utilise 'lifePoints' à la place de 'hp'
      imageUrl: pokemon.imageUrl,
      rarity: pokemon.weakness.name // Met la faiblesse comme 'rarity' (à adapter si nécessaire)
    }));

  } catch (error) {
    console.error('Erreur lors de la récupération des cartes Pokémon :', error);
  }
};



onMounted(() => {
  fetchPokemonData();
});
</script>

<style scoped>
.card-list {
  padding: 16px;
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
