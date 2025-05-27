<template>
  <div class="main-wrapper">
    <!-- 🖼️ Hero Swiper Section -->
    <div class="hero-swiper section-observe">
      <Swiper
        :modules="modules"
        :pagination="{ clickable: true }"
        :autoplay="{ delay: 4000 }"
        loop
      >
        <SwiperSlide
          v-for="(slide, index) in slides"
          :key="index"
        >
          <img
            :src="slide.src"
            :alt="slide.alt"
            class="hero-image"
          />
        </SwiperSlide>
      </Swiper>
    </div>

    <div class="below-hero-bg">
      <div class="main-container">
        <!-- 베스트셀러 슬라이더 섹션 -->
        <div
          class="best-container section-observe"
          ref="bestSection"
        >
          <BestSeller :books="bestSellers" />
        </div>

        <!-- 진행 중인 챌린지 -->
        <div class="row-section">
          <div
            class="half-section challenge-box section-observe"
            ref="challengeSection"
          >
            <h2 class="section-title">진행 중인 챌린지</h2>
            <div class="slider-wrapper">
              <button
                class="slide-btn left"
                @click="slideLeftChallenge"
              >‹</button>
              <div class="slider" ref="challengeSlider">
                <div
                  class="card"
                  v-for="c in challenges"
                  :key="c.id"
                >
                  <h4 class="title">{{ c.title }}</h4>
                  <p class="intro">{{ c.intro }}</p>
                </div>
              </div>
              <button
                class="slide-btn right"
                @click="slideRightChallenge"
              >›</button>
            </div>
            <RouterLink
              to="/challenge/list"
              class="more-link"
            >→ 더 보기</RouterLink>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { RouterLink } from 'vue-router'

// Swiper for Hero
import { Swiper, SwiperSlide } from 'swiper/vue'
import 'swiper/css'
import 'swiper/css/pagination'
import { Pagination, Autoplay } from 'swiper/modules'
const modules = [Pagination, Autoplay]
const slides = [
  { src: '/img/booknest1.png', alt: '배너1' },
  { src: '/img/bn2.png',    alt: '배너2' },
]

// BestSellerSlider 컴포넌트 (중앙 확대 슬라이드)
import BestSeller from '@/components/main/BestSeller.vue'
const bestSellers = ref([])

// 챌린지 데이터
const challenges = ref([])

// 슬라이드 제어
const slideLeftChallenge = () => {
  const s = document.querySelector('.challenge-box .slider')
  if (s) s.scrollLeft -= 400
}
const slideRightChallenge = () => {
  const s = document.querySelector('.challenge-box .slider')
  if (s) s.scrollLeft += 400
}

// 데이터 로드 및 애니메이션
onMounted(() => {
  // 베스트셀러 가져오기
  axios.get('http://127.0.0.1:8000/api/v1/books/bestsellers/')
    .then(res => bestSellers.value = res.data)
    .catch(err => console.error('베스트셀러 로드 실패:', err))

  // 챌린지 가져오기
  axios.get('http://127.0.0.1:8000/api/v1/challenges/')
    .then(res => challenges.value = res.data)
    .catch(err => console.error('챌린지 로드 실패:', err))

  // 섹션 애니메이션 옵저버
  const observer = new IntersectionObserver(entries => {
    entries.forEach(entry => {
      entry.target.classList.toggle('show', entry.isIntersecting)
    })
  }, { threshold: 0.15 })

  document.querySelectorAll('.section-observe')
          .forEach(el => observer.observe(el))
})
</script>

<style scoped>
* {
  font-family: 'NanumSquare', sans-serif;
}

/* 🖼️ Hero Banner 크기 조절 */
.hero-swiper {
  width: 100%;
  max-height: 800px;     /* 원하는 최대 높이(px) */
  overflow: hidden;
}
.hero-swiper .swiper-slide {
  width: 100% !important;
  height: auto  !important;
}
.hero-image {
  width: 100% !important;
  height: auto  !important;
  max-height: 800px !important;  /* Hero 컨테이너와 일치 */
  object-fit: contain !important; /* 잘리지 않고 전체 보이기 */
  display: block;
  margin: 0 auto;
}

/* Swiper 기본 가로폭 */
.swiper-container {
  width: 100% !important;
}

/* 나머지 레이아웃 및 스타일 (변경 없음) */
.main-wrapper { width: 100%; }

.below-hero-bg {
  position: relative;
  padding-top: 0.8rem;
  background-color: rgba(197, 255, 233, 0.39);
  
}

.below-hero-bg::before,
.below-hero-bg::after {
  content: '';
  position: absolute;
  top: 0;
  height: 100%;
  width: 1px;
  background: rgba(150, 150, 150, 0.5);
}


.main-container {
  max-width: 1400px;
  margin: 0 auto;
  padding: 1rem;
}

.row-section {
  display: flex;
  gap: 2rem;
  justify-content: space-between;
  flex-wrap: wrap;
  margin-top: 4rem;
  position: relative;
}
.row-section::after {
  content: '';
  display: block;
  height: 1px;
  background: #ddd;
  width: 100%;
  margin: 2rem 0 0;
}

.half-section {
  flex: 1 1 0;
  min-width: 300px;
  background-color: #fff1e1;
  border-radius: 16px;
  padding: 1.5rem 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.section-title {
  font-size: 1.5rem;
  font-weight: 500;
  margin-bottom: 1.2rem;
}
.slider-wrapper {
  display: flex;
  align-items: center;
  position: relative;
  overflow: hidden;
  margin-top: 0.5rem;
}
.slider {
  display: flex;
  gap: 1rem;
  background: none;
  overflow-x: auto;
  scroll-behavior: smooth;
  padding: 0 0.5rem;
}
.card {
  min-width: 160px;
  max-width: 160px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  padding: 0.8rem;
  flex-shrink: 0;
  transition: transform 0.2s;
}
.card:hover {
  transform: translateY(-6px);
}
.more-link {
  display: block;
  text-align: right;
  margin-top: 1rem;
  color: #fc47b0;
  font-weight: bold;
  font-size: 0.9rem;
}
.slide-btn {
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 50%;
  width: 28px;
  height: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 10;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
.slide-btn.left {
  left: 0.3rem;
}
.slide-btn.right {
  right: 0.3rem;
}

/* 섹션 애니메이션 */
.section-observe {
  opacity: 0;
  transform: translateY(40px);
  transition: all 0.8s ease;
}
.section-observe.show {
  opacity: 1;
  transform: translateY(0);
}
</style>
