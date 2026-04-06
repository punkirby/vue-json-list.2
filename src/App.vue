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
  font-family: 'Poppins', 'Segoe UI', sans-serif;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 25%, #0f3460 50%, #1a1a2e 75%, #533483 100%);
  background-size: 400% 400%;
  animation: gradientShift 15s ease infinite;
  color: #e0e0e0;
  min-height: 100vh;
}

@keyframes gradientShift {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 3rem 1.5rem;
}

header {
  text-align: center;
  margin-bottom: 3rem;
  color: #ffffff;
  animation: slideDown 0.8s ease-out;
  text-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
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
  font-size: clamp(2.5rem, 6vw, 3.8rem);
  font-weight: 800;
  background: linear-gradient(45deg, #ffffff 0%, #a8e6cf 25%, #4ecdc4 50%, #45b7d1 75%, #96ceb4 100%);
  background-size: 300% 300%;
  animation: textGradient 3s ease infinite;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: -1px;
  text-shadow: none;
}

@keyframes textGradient {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

header p {
  margin: 0;
  font-size: 1.2rem;
  opacity: 0.95;
  font-weight: 400;
  letter-spacing: 0.5px;
  color: rgba(255, 255, 255, 0.9);
}

.loading {
  padding: 3rem;
  border-radius: 24px;
  background: rgba(30, 30, 46, 0.95);
  box-shadow: 0 25px 80px rgba(0, 0, 0, 0.3);
  text-align: center;
  font-size: 1.2rem;
  color: #a8e6cf;
  font-weight: 600;
  backdrop-filter: blur(20px);
  animation: fadeIn 0.5s ease-out, pulse 2s ease-in-out infinite;
  border: 2px solid transparent;
  background-image: linear-gradient(rgba(30, 30, 46, 0.95), rgba(30, 30, 46, 0.95)), linear-gradient(45deg, #a8e6cf, #4ecdc4, #45b7d1, #96ceb4);
  background-origin: border-box;
  background-clip: content-box, border-box;
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
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
  padding: 2.5rem;
  border-radius: 24px;
  background: linear-gradient(135deg, #ff6b6b 0%, #e17055 100%);
  box-shadow: 0 25px 80px rgba(255, 107, 107, 0.3);
  color: white;
  font-weight: 600;
  backdrop-filter: blur(20px);
  border: 2px solid rgba(255, 255, 255, 0.2);
  animation: shake 0.5s ease-out;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.error strong {
  display: block;
  margin-bottom: 0.5rem;
  font-size: 1.2rem;
}

.list-section ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
}

.card {
  padding: 2.5rem;
  border-radius: 28px;
  background: rgba(30, 30, 46, 0.95);
  box-shadow: 0 30px 60px rgba(0, 0, 0, 0.3);
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
  animation: cardEnter 0.8s ease-out;
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

@keyframes cardEnter {
  from {
    opacity: 0;
    transform: translateY(30px) rotate(-2deg);
  }
  to {
    opacity: 1;
    transform: translateY(0) rotate(0deg);
  }
}

.card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 6px;
  background: linear-gradient(90deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4, #ffeaa7);
  background-size: 300% 100%;
  animation: borderGlow 4s ease infinite;
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.4s ease-out;
}

@keyframes borderGlow {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

.card:hover {
  transform: translateY(-12px) scale(1.02);
  box-shadow: 0 40px 100px rgba(0, 0, 0, 0.4);
  border-color: rgba(78, 205, 196, 0.3);
}

.card:hover::before {
  transform: scaleX(1);
}

.card h2 {
  margin: 0 0 1.2rem;
  font-size: 1.6rem;
  font-weight: 700;
  background: linear-gradient(135deg, #ff6b6b, #4ecdc4, #45b7d1);
  background-size: 200% 200%;
  animation: titleGradient 3s ease infinite;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  line-height: 1.3;
}

@keyframes titleGradient {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

.card p {
  margin: 0 0 1.8rem;
  color: #b8c5d1;
  line-height: 1.8;
  font-size: 1rem;
  font-weight: 400;
}

.meta {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  margin-bottom: 2rem;
  padding-bottom: 1.8rem;
  border-bottom: 2px solid rgba(168, 230, 207, 0.1);
}

.meta span {
  display: flex;
  align-items: center;
  font-size: 0.95rem;
  color: #a8e6cf;
  font-weight: 600;
  background: rgba(168, 230, 207, 0.1);
  padding: 0.5rem 1rem;
  border-radius: 20px;
  border: 1px solid rgba(168, 230, 207, 0.2);
}

.meta span::before {
  content: '✨';
  margin-right: 0.8rem;
  font-size: 0.8rem;
  color: #a8e6cf;
}

.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.8rem;
}

.tags span {
  display: inline-block;
  padding: 0.6rem 1.2rem;
  background: linear-gradient(135deg, #4ecdc4, #45b7d1, #96ceb4);
  background-size: 200% 200%;
  animation: tagGlow 4s ease infinite;
  color: #1a1a2e;
  border-radius: 25px;
  font-size: 0.9rem;
  font-weight: 600;
  border: none;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(78, 205, 196, 0.3);
  cursor: pointer;
}

@keyframes tagGlow {
  0%, 100% { background-position: 0% 50%; box-shadow: 0 4px 15px rgba(78, 205, 196, 0.3); }
  50% { background-position: 100% 50%; box-shadow: 0 4px 15px rgba(78, 205, 196, 0.5); }
}

.tags span:hover {
  transform: translateY(-2px) scale(1.05);
  box-shadow: 0 6px 20px rgba(78, 205, 196, 0.4);
  background-position: 100% 50%;
}

@media (max-width: 768px) {
  .app {
    padding: 2rem 1rem;
  }

  header h1 {
    font-size: 2.2rem;
  }

  .list-section ul {
    grid-template-columns: 1fr;
    gap: 1.8rem;
  }

  .card {
    padding: 2rem;
  }

  .meta {
    gap: 1rem;
  }

  .tags span {
    padding: 0.5rem 1rem;
    font-size: 0.85rem;
  }
}

@media (max-width: 480px) {
  .app {
    padding: 1.5rem 1rem;
  }

  header {
    margin-bottom: 2rem;
  }

  header h1 {
    font-size: 2rem;
  }

  .card {
    padding: 1.5rem;
  }

  .card h2 {
    font-size: 1.4rem;
  }
}
</style>
