<template>
  <div class="bestseller-section">
    <h2 class="section-title">📚 베스트셀러</h2>

    <div class="category-tabs">
      <button
        v-for="g in genres"
        :key="g"
        :class="{ active: activeGenre === g }"
        @click="filterByCategory(g)"
      >{{ g }}</button>
    </div>

    <div class="slider-wrapper">
      <button class="slide-btn left" @click="slideLeft">‹</button>

      <div class="book-slider" ref="slider">
        <div class="book-card" v-for="book in filteredBooks" :key="book.id">
          <img :src="book.cover" :alt="book.title" class="book-cover" />
          <p class="book-title">{{ book.title }}</p>
        </div>
      </div>

      <button class="slide-btn right" @click="slideRight">›</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

const books = ref([])
const genres = ref([])
const activeGenre = ref('전체')
const slider = ref(null)

const filteredBooks = computed(() => {
  if (activeGenre.value === '전체') return books.value
  return books.value.filter(b => b.category?.name === activeGenre.value)
})

const filterByCategory = (genre) => {
  activeGenre.value = genre
}

const slideLeft = () => {
  slider.value.scrollLeft -= 400
}

const slideRight = () => {
  slider.value.scrollLeft += 400
}

onMounted(() => {
  axios.get('http://127.0.0.1:8000/api/v1/books/')
    .then(res => {
      books.value = res.data
      genres.value = ['전체', ...new Set(res.data.map(b => b.category?.name).filter(Boolean))]
    })
})
</script>

<style scoped>
.bestseller-section {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

.section-title {
  font-size: 1.6rem;
  font-weight: bold;
  text-align: center;
  margin-bottom: 1.5rem;
}

.category-tabs {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-bottom: 2rem;
}

.category-tabs button {
  padding: 0.4rem 1rem;
  border: 1px solid #ccc;
  border-radius: 9999px;
  background: white;
  cursor: pointer;
  transition: all 0.3s;
}

.category-tabs button.active,
.category-tabs button:hover {
  background-color: #fc47b0;
  color: white;
  font-weight: bold;
}

.slider-wrapper {
  display: flex;
  align-items: center;
  position: relative;
  overflow: hidden;
}

.book-slider {
  display: flex;
  gap: 1.5rem;
  overflow-x: auto;
  scroll-behavior: smooth;
  padding: 0 1rem;
}

.book-card {
  min-width: 180px;
  max-width: 180px;
  background: #fff;
  border-radius: 16px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  padding: 1rem;
  text-align: center;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  flex-shrink: 0;
}

.book-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 6px 16px rgba(0,0,0,0.12);
}

.book-cover {
  width: 100%;
  height: auto;
  object-fit: contain;
  border-radius: 10px;
}

.book-title {
  margin-top: 0.8rem;
  font-size: 0.95rem;
  color: #333;
  font-weight: 500;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
}

.slide-btn {
  background: white;
  border: 1px solid #ccc;
  border-radius: 50%;
  font-size: 1.5rem;
  width: 36px;
  height: 36px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 10;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

.slide-btn.left {
  left: 0.5rem;
}

.slide-btn.right {
  right: 0.5rem;
}
</style>
