<template>
  <div class="deck-list">
    <h2>Mes Decks</h2>
    <n-data-table :columns="columns" :data="filteredDecks" :bordered="false" />
    
    <n-modal v-model:show="showModal" title="Détails du Deck">
      <n-card>
        <h3>{{ selectedDeck?.name }}</h3>
        <n-grid :cols="3" x-gap="12" y-gap="12">
          <n-grid-item v-for="pokemon in selectedDeck?.cards" :key="pokemon.id">
            <n-card>
              <img :src="pokemon.imageUrl" alt="{{ pokemon.name }}" class="card-image" />
              <p>{{ pokemon.name }}</p>
            </n-card>
          </n-grid-item>
        </n-grid>
      </n-card>
    </n-modal>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';

interface PokemonCard {
  id: string;
  name: string;
  imageUrl: string;
}

interface Deck {
  id: string;
  name: string;
  userId: string;
  cards: PokemonCard[];
}

const userId = "utilisateur-connecte-id"; // Remplacez par la vraie méthode d'obtention de l'ID utilisateur
const decks = ref<Deck[]>([]);
const showModal = ref(false);
const selectedDeck = ref<Deck | null>(null);

const fetchDecks = async () => {
  try {
    const response = await axios.get('https://pokemon-api-seyrinian-production.up.railway.app/decks');
    decks.value = response.data;
  } catch (error) {
    console.error('Erreur lors de la récupération des decks :', error);
  }
};

const filteredDecks = computed(() => {
  return decks.value.filter(deck => deck.userId === userId);
});

const showDeckDetails = (deck: Deck) => {
  selectedDeck.value = deck;
  showModal.value = true;
};

const deleteDeck = async (deckId: string) => {
  try {
    await axios.delete(`https://pokemon-api-seyrinian-production.up.railway.app/decks/${deckId}`);
    decks.value = decks.value.filter(deck => deck.id !== deckId);
  } catch (error) {
    console.error('Erreur lors de la suppression du deck :', error);
  }
};

const columns = [
  { title: 'Nom du Deck', key: 'name' },
  { 
    title: 'Actions', 
    key: 'actions',
    render(row: Deck) {
      return [
        h('n-button', { onClick: () => showDeckDetails(row) }, 'Voir'),
        h('n-button', { onClick: () => deleteDeck(row.id), type: 'error' }, 'Supprimer')
      ];
    }
  }
];

onMounted(() => {
  fetchDecks();
});
</script>

<style scoped>
.deck-list {
  padding: 16px;
}

.card-image {
  width: 100%;
  height: auto;
  border-radius: 8px;
}
</style>