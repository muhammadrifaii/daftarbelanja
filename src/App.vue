<template>
  <div class="container">
    <h1 class="title">🛒 Daftar Belanja Harian</h1>

    <div class="summary">
      <span>Total: {{ items.length }}</span>
      <span>Selesai: {{ finishedItems.length }}</span>
      <span>Total Biaya: <strong>Rp {{ totalCost.toLocaleString() }}</strong></span>
    </div>

    <!-- Form Tambah -->
    <div class="form">
      <input v-model="newItem.name" placeholder="Nama item" />
      <input v-model.number="newItem.price" type="number" placeholder="Harga (Rp)" />

      <select v-model="newItem.category">
        <option value="Makanan">🍎 Makanan</option>
        <option value="Kebersihan">🧼 Minuman</option>
      </select>


      <button class="add-btn" @click="addItem" :disabled="!newItem.name">➕ Tambah ke Daftar</button>
    </div>

    <!-- Filter -->
    <div class="filters">
      <button @click="filter = 'all'" :class="{ active: filter === 'all' }">Semua</button>
      <button @click="filter = 'pending'" :class="{ active: filter === 'pending' }">Belum Selesai</button>
      <button @click="filter = 'done'" :class="{ active: filter === 'done' }">Selesai</button>
    </div>

    <!-- List Belanjaan -->
    <ul class="shopping-list">
      <li v-for="(item, i) in filteredItems" :key="i" :class="[item.priority.toLowerCase(), { done: item.done }]">
        <label>
          <input type="checkbox" v-model="item.done" />
          <span>{{ item.name }}</span>
        </label>
        <div class="info">
          <span>{{ item.category }}</span>
          <span>Rp {{ item.price.toLocaleString() }}</span>
          <span>{{ item.priority }}</span>
        </div>
        <button @click="removeItem(i)" class="delete">Hapus</button>
      </li>
    </ul>

    <!-- Budget -->
    <div class="budget">
      <input v-model.number="budget" placeholder="Budget Belanja" />
      <p :class="budget >= totalCost ? 'within-budget' : 'over-budget'">
        {{ budget >= totalCost
          ? `✅ Masih dalam budget, sisa Rp ${budget - totalCost}`
          : `⚠️ Melebihi budget sebesar Rp ${totalCost - budget}` }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const items = ref([
  { name: 'Beras 5kg', category: 'Makanan', price: 45000, priority: 'Tinggi', done: false },
  { name: 'Minyak Goreng 2L', category: 'Makanan', price: 28000, priority: 'Sedang', done: true },
  { name: 'Sabun Cuci Piring', category: 'Kebersihan', price: 8000, priority: 'Rendah', done: false },
])

const newItem = ref({ name: '', category: 'Makanan', price: 0, priority: 'Sedang', done: false })
const budget = ref(100000)
const filter = ref('all')

const totalCost = computed(() =>
  items.value.reduce((sum, item) => item.done ? sum + item.price : sum, 0)
)

const finishedItems = computed(() => items.value.filter(item => item.done))

const filteredItems = computed(() => {
  if (filter.value === 'done') return items.value.filter(i => i.done)
  if (filter.value === 'pending') return items.value.filter(i => !i.done)
  return items.value
})

function addItem() {
  items.value.push({ ...newItem.value })
  newItem.value.name = ''
  newItem.value.price = 0
}

function removeItem(index) {
  items.value.splice(index, 1)
}
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: auto;
  padding: 2rem;
  font-family: Arial, sans-serif;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 0 12px rgba(0, 0, 0, 0.1);
}
.title {
  color: #2c3e50;
  margin-bottom: 1rem;
  text-align: center;
}
.summary span {
  margin-right: 1rem;
  font-weight: bold;
}
.form input,
.form select {
  margin-right: 0.5rem;
  padding: 0.4rem;
}
.add-btn {
  padding: 0.5rem 1rem;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
.add-btn:disabled {
  background-color: #95a5a6;
  cursor: not-allowed;
}
.filters button {
  margin: 0.2rem;
  padding: 0.4rem 0.8rem;
  border: 1px solid #ccc;
  background-color: #eee;
  cursor: pointer;
}
.filters .active {
  background-color: #3498db;
  color: white;
  font-weight: bold;
}
.shopping-list {
  list-style: none;
  padding: 0;
  margin-top: 1rem;
}
.shopping-list li {
  padding: 0.8rem;
  margin-bottom: 0.5rem;
  border-left: 5px solid #bdc3c7;
  border-radius: 6px;
  background-color: #f9f9f9;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.shopping-list li.done {
  text-decoration: line-through;
  color: #aaa;
}
.shopping-list li.tinggi {
  border-color: #e74c3c;
}
.shopping-list li.sedang {
  border-color: #f1c40f;
}
.shopping-list li.rendah {
  border-color: #2ecc71;
}
.shopping-list .info span {
  display: inline-block;
  margin: 0 0.5rem;
}
.delete {
  background-color: #e74c3c;
  color: white;
  border: none;
  padding: 0.4rem 0.6rem;
  border-radius: 4px;
  cursor: pointer;
}
.budget input {
  margin-top: 1rem;
  padding: 0.4rem;
  width: 100%;
}
.within-budget {
  color: green;
  margin-top: 0.5rem;
}
.over-budget {
  color: red;
  margin-top: 0.5rem;
}
</style>