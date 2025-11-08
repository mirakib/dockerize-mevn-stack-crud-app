<template>
  <div class="app-container">
    <header class="app-header">
      <h1>User Manager</h1>
    </header>

    <main class="content">
      <section class="panel form-panel">
        <div class="card">
          <h2>{{ editingId ? 'Edit' : 'Add' }} User</h2>
          <form @submit.prevent="addUser">
            <input v-model="name" placeholder="Name" required />
            <input v-model="email" placeholder="Email" required />
            <div class="actions">
              <button type="submit" class="btn primary">{{ editingId ? 'Save' : 'Add' }}</button>
              <button type="button" class="btn" v-if="editingId" @click="cancelEdit">Cancel</button>
            </div>
          </form>
        </div>
      </section>

      <section class="panel list-panel">
        <div class="card">
          <h2>Users</h2>
          <ul>
            <li v-for="user in users" :key="user._id" class="user-item">
              <div class="user-info">
                <strong>{{ user.name }}</strong>
                <small>{{ user.email }}</small>
              </div>
              <div class="user-actions">
                <button class="btn" @click="editUser(user)">Edit</button>
                <button class="btn danger" @click="deleteUser(user._id)">Delete</button>
              </div>
            </li>
          </ul>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const users = ref([])
const name = ref('')
const email = ref('')
const editingId = ref(null)

const API_URL = '/api/users'

async function fetchUsers() {
  try {
    const res = await fetch(API_URL)
    users.value = await res.json()
  } catch (err) {
    console.error('Failed to fetch users', err)
  }
}

async function addUser() {
  const method = editingId.value ? 'PUT' : 'POST'
  const url = editingId.value ? `${API_URL}/${editingId.value}` : API_URL

  await fetch(url, {
    method,
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: name.value, email: email.value })
  })

  name.value = ''
  email.value = ''
  editingId.value = null
  fetchUsers()
}

function editUser(user) {
  name.value = user.name
  email.value = user.email
  editingId.value = user._id
}

function cancelEdit() {
  name.value = ''
  email.value = ''
  editingId.value = null
}

async function deleteUser(id) {
  await fetch(`${API_URL}/${id}`, { method: 'DELETE' })
  fetchUsers()
}

onMounted(fetchUsers)
</script>

<style scoped>
:root{
  --bg-1: #0f172a; /* deep navy */
  --bg-2: linear-gradient(135deg,#0f172a 0%, #0b3a66 100%);
  --card: #ffffff;
  --muted: #6b7280;
  --accent: #06b6d4; /* teal */
  --accent-2: #7c3aed; /* purple */
  --danger: #ef4444;
}

.app-container{
  min-height: 100vh;
  background: var(--bg-2);
  color: #f8fafc;
  padding: 32px;
  box-sizing: border-box;
  font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
}

.app-header h1{
  margin: 0 0 20px 0;
  text-align: center;
  font-weight: 600;
  letter-spacing: -0.5px;
}

.content{
  display: flex;
  gap: 24px;
  max-width: 1000px;
  margin: 0 auto;
  align-items: flex-start;
}

.panel{
  flex: 1 1 0;
}
.form-panel{ flex-basis: 38%; }
.list-panel{ flex-basis: 62%; }

.card{
  background: rgba(255,255,255,0.06);
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 6px 18px rgba(2,6,23,0.5);
  border: 1px solid rgba(255,255,255,0.04);
}

.card h2{
  margin-top: 0;
  color: #e6eef8;
}

input{
  display: block;
  width: 100%;
  padding: 10px 12px;
  margin: 10px 0;
  border-radius: 8px;
  border: 1px solid rgba(255,255,255,0.08);
  background: rgba(255,255,255,0.02);
  color: #e6eef8;
}

.actions{
  display: flex;
  gap: 10px;
  justify-content: flex-start;
  margin-top: 8px;
}

.btn{
  padding: 8px 12px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  background: rgba(255,255,255,0.06);
  color: #e6eef8;
}
.btn.primary{
  background: linear-gradient(90deg,var(--accent),var(--accent-2));
  box-shadow: 0 6px 12px rgba(12,74,110,0.25);
}
.btn.danger{
  background: rgba(239,68,68,0.9);
}

ul{ list-style: none; padding: 0; margin: 0; }
.user-item{
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px;
  border-radius: 8px;
  background: rgba(255,255,255,0.02);
  margin-bottom: 10px;
}
.user-info strong{ display:block; color: #fff }
.user-info small{ color: var(--muted) }
.user-actions button{ margin-left: 8px }

@media (max-width: 800px){
  .content{ flex-direction: column; }
  .form-panel, .list-panel{ flex-basis: auto }
}
</style>
