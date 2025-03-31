<template>
  <div class="card-list">
    <n-input v-model="searchQuery" placeholder="Rechercher un Pokémon..." class="search-input" />
    
    <n-grid :cols="3" x-gap="12" y-gap="12">
      <n-grid-item v-for="pokemon in filteredPokemonList" :key="pokemon.id" @click="addToDeck(pokemon)">
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

    <h2>Mon Deck</h2>
    <n-input v-model="deckName" placeholder="Nom du deck..." class="deck-input" />
    <n-grid :cols="3" x-gap="12" y-gap="12">
      <n-grid-item v-for="pokemon in deck" :key="pokemon.id" @click="removeFromDeck(pokemon)">
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
    <n-button type="primary" @click="saveDeck">Sauvegarder le deck</n-button>
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
const deck = ref<PokemonCard[]>([]);
const searchQuery = ref<string>('');
const deckName = ref<string>('');

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

const addToDeck = (pokemon: PokemonCard) => {
  if (!deck.value.find(p => p.id === pokemon.id)) {
    deck.value.push(pokemon);
  }
};

const removeFromDeck = (pokemon: PokemonCard) => {
  deck.value = deck.value.filter(p => p.id !== pokemon.id);
};

const saveDeck = async () => {
  if (!deckName.value.trim()) {
    alert("Veuillez entrer un nom pour votre deck.");
    return;
  }
  try {
    await axios.post('https://pokemon-api-seyrinian-production.up.railway.app/save-deck', {
      name: deckName.value,
      cards: deck.value.map(pokemon => pokemon.id)
    });
    alert("Deck sauvegardé avec succès !");
  } catch (error) {
    console.error("Erreur lors de la sauvegarde du deck :", error);
  }
};

const filteredPokemonList = computed(() => {
  return pokemonList.value.filter(pokemon => 
    pokemon.name?.toLowerCase().includes(searchQuery.value.toLowerCase())
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

.search-input, .deck-input {
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

h2 {
  margin-top: 24px;
}

n-grid-item:hover {
  cursor: pointer;
}
</style>
