<template>
  <div class="app-container">
    <header>
      <button @click="openColumnModal">Adicionar Coluna</button>
    </header>

    <main class="board">
      <div class="column" v-for="column in columns" :key="column.id">
        <div class="column-header">
          <h2>{{ column.title }}</h2>
          <button @click="openCardModal(column)">Adicionar Card</button>
        </div>

        <div class="cards">
          <Draggable
            v-model="column.cards"
            :group="'cards'"
            item-key="id"
          >
            <template #item="{ element }">
              <div class="card">
                <h3>{{ element.title }}</h3>
                <p>{{ element.date }}</p>
              </div>
            </template>
          </Draggable>
        </div>
      </div>
    </main>

    <!-- Modal Coluna -->
    <div v-if="showColumnModal" class="modal">
      <div class="modal-content">
        <h3>Criar Coluna</h3>
        <input v-model="newColumnTitle" placeholder="Título da coluna" />
        <button @click="addColumn">Criar</button>
        <button @click="closeColumnModal">Cancelar</button>
      </div>
    </div>

    <!-- Modal Card -->
    <div v-if="showCardModal" class="modal">
      <div class="modal-content">
        <h3>Criar Card</h3>
        <input v-model="newCardTitle" placeholder="Título do card" />
        <input v-model="newCardDate" type="date" />
        <button @click="addCard">Criar</button>
        <button @click="closeCardModal">Cancelar</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'
import Draggable from 'vuedraggable'

// Lista de colunas
const columns = reactive([])

// Modais
const showColumnModal = ref(false)
const showCardModal = ref(false)

// Inputs
const newColumnTitle = ref('')
const newCardTitle = ref('')
const newCardDate = ref('')

// Coluna atual para adicionar cards
let currentColumn = null

// Abrir/fechar modais
function openColumnModal() { showColumnModal.value = true }
function closeColumnModal() { showColumnModal.value = false; newColumnTitle.value = '' }

function openCardModal(column) {
  currentColumn = column
  showCardModal.value = true
}
function closeCardModal() {
  showCardModal.value = false
  newCardTitle.value = ''
  newCardDate.value = ''
  currentColumn = null
}

// Funções
function addColumn() {
  if (newColumnTitle.value.trim() === '') return
  columns.push({
    id: Date.now(),
    title: newColumnTitle.value,
    cards: [] // inicializa array de cards
  })
  closeColumnModal()
}

function addCard() {
  if (!currentColumn || newCardTitle.value.trim() === '') return
  currentColumn.cards.push({
    id: Date.now(),
    title: newCardTitle.value,
    date: newCardDate.value
  })
  closeCardModal()
}
</script>

<style>
.app-container {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.board {
  display: flex;
  flex-direction: row; /* horizontal */
  gap: 20px;
  margin-top: 20px;
  overflow-x: auto; /* permite scroll horizontal */
  padding-bottom: 20px;
}

.column {
  background: #f0f0f0;
  padding: 10px;
  border-radius: 8px;
  width: 250px;
  flex-shrink: 0; /* evita que encolha com muitas colunas */
}

.column-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.cards {
  margin-top: 10px;
  min-height: 50px; /* espaço para arrastar */
}

.card {
  background: #fff;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 6px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.2);
  cursor: grab;
}

.card:active {
  cursor: grabbing;
}

/* Modal simples */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
}
</style>
