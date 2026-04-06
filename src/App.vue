<template>
  <div class="app">
    <header>
      <h1>Exploración de destinos culturales</h1>
      <p>Lista de lugares recomendados para descubrir arte, historia y gastronomía.</p>
    </header>

    <section v-if="error" class="error">
      <strong>Error:</strong> {{ error }}
    </section>

    <section v-else-if="items.length === 0" class="loading">
      Cargando la lista desde el archivo JSON...
    </section>

    <section v-else class="list-section">
      <ul>
        <li v-for="item in items" :key="item.id" class="card">
          <h2>{{ item.name }}</h2>
          <p>{{ item.description }}</p>
          <div class="meta">
            <span>País: {{ item.country }}</span>
            <span>Año fundado: {{ item.founded }}</span>
          </div>
          <div class="tags">
            <span v-for="tag in item.tags" :key="tag">#{{ tag }}</span>
          </div>
        </li>
      </ul>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

interface Destination {
  id: number
  name: string
  country: string
  founded: number
  description: string
  tags: string[]
}

const items = ref<Destination[]>([])
const error = ref('')

onMounted(async () => {
  try {
    const response = await fetch('/data.json')
    if (!response.ok) {
      throw new Error(`No se pudo cargar data.json: ${response.statusText}`)
    }
    items.value = await response.json()
  } catch (err) {
    error.value = err instanceof Error ? err.message : String(err)
  }
})
</script>

<style scoped>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: 'Segoe UI', Trebuchet MS, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #1a202c;
  min-height: 100vh;
}

.app {
  max-width: 1000px;
  margin: 0 auto;
  padding: 3rem 1.5rem;
}

header {
  text-align: center;
  margin-bottom: 3rem;
  color: black;
  animation: slideDown 0.8s ease-out;
}

@keyframes slideDown {
  from {
    opacity: 0;
    transform: translateY(-30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

header h1 {
  margin: 0 0 0.8rem;
  font-size: clamp(2.2rem, 5vw, 3.2rem);
  font-weight: 700;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  letter-spacing: -0.5px;
}

header p {
  margin: 0;
  font-size: 1.1rem;
  opacity: 0.95;
  font-weight: 300;
  letter-spacing: 0.3px;
}

.loading {
  padding: 2.5rem;
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.95);
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
  text-align: center;
  font-size: 1.1rem;
  color: #667eea;
  font-weight: 500;
  backdrop-filter: blur(10px);
  animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.error {
  padding: 2rem;
  border-radius: 16px;
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
  box-shadow: 0 20px 60px rgba(245, 87, 108, 0.2);
  color: white;
  font-weight: 500;
  backdrop-filter: blur(10px);
}

.error strong {
  display: block;
  margin-bottom: 0.5rem;
  font-size: 1.1rem;
}

.list-section ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: grid;
  gap: 1.8rem;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
}

.card {
  padding: 2rem;
  border-radius: 20px;
  background: white;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.1);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
  animation: cardEnter 0.6s ease-out;
}

@keyframes cardEnter {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(90deg, #667eea, #764ba2);
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.3s ease-out;
}

.card:hover {
  transform: translateY(-8px);
  box-shadow: 0 35px 70px rgba(102, 126, 234, 0.2);
}

.card:hover::before {
  transform: scaleX(1);
}

.card h2 {
  margin: 0 0 1rem;
  font-size: 1.5rem;
  font-weight: 600;
  color: #2d3748;
  background: linear-gradient(135deg, #667eea, #764ba2);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.card p {
  margin: 0 0 1.5rem;
  color: #4a5568;
  line-height: 1.7;
  font-size: 0.95rem;
}

.meta {
  display: flex;
  flex-wrap: wrap;
  gap: 1.2rem;
  margin-bottom: 1.5rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid #e2e8f0;
}

.meta span {
  display: flex;
  align-items: center;
  font-size: 0.9rem;
  color: #718096;
  font-weight: 500;
}

.meta span::before {
  content: '◆';
  margin-right: 0.6rem;
  color: #667eea;
  font-size: 0.7rem;
}

.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.6rem;
}

.tags span {
  display: inline-block;
  padding: 0.5rem 1rem;
  background: linear-gradient(135deg, #667eea15, #764ba215);
  color: #667eea;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  border: 1px solid rgba(102, 126, 234, 0.3);
  transition: all 0.2s ease;
}

.tags span:hover {
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  transform: scale(1.05);
  border-color: transparent;
}

@media (max-width: 768px) {
  .app {
    padding: 2rem 1rem;
  }

  header h1 {
    font-size: 2rem;
  }

  .list-section ul {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .card {
    padding: 1.5rem;
  }
}
</style>
