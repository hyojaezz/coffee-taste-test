<template>
  <div class="app">
    <div class="container">

      <!-- 시작 화면 -->
      <div v-if="step === 'start'" class="card start-card">
        <h1>☕ 내 취향 커피 테스트</h1>
        <p class="sub-text">
          8가지 질문에 답하고
          나와 가장 잘 맞는 커피를 찾아보세요.
        </p>
        <button class="primary-btn" @click="startTest">테스트 시작</button>
      </div>

      <!-- 질문 화면 -->
      <div v-else-if="step === 'question'" class="card">
        <div class="progress-wrap">
          <div class="progress-text">
            {{ currentQuestionIndex + 1 }} / {{ questions.length }}
          </div>
          <div class="progress-bar">
            <div
              class="progress-fill"
              :style="{ width: progressPercent + '%' }"
            ></div>
          </div>
        </div>

        <h2 class="question-title">
          ☕ {{ currentQuestion.question }}
        </h2>

        <div class="options">
          <button
            v-for="option in currentQuestion.options"
            :key="option.text"
            class="option-btn"
            @click="selectOption(option.type)"
          >
            {{ option.text }}
          </button>
        </div>
      </div>

      <!-- 결과 화면 -->
      <div v-else-if="step === 'result'" class="card result-card">
        <p class="result-label">☕ 추천 결과</p>

        <!-- 🔥 핵심: 이모지 크게 표시 -->
        <div class="result-image-wrap">
          <img :src="result.image" :alt="result.title" class="result-image" />
        </div>

        <h2>{{ result.title }}</h2>
        <p class="result-desc">{{ result.description }}</p>

        <div class="result-info">
          <div class="info-box">
            <span class="info-label">추천 스타일</span>
            <strong>{{ result.style }}</strong>
          </div>
          <div class="info-box">
            <span class="info-label">추천 온도</span>
            <strong>{{ result.temperature }}</strong>
          </div>
          <div class="info-box">
            <span class="info-label">어울리는 시간대</span>
            <strong>{{ result.time }}</strong>
          </div>
          <div class="info-box">
            <span class="info-label">추천 디저트</span>
            <strong>{{ result.dessert }}</strong>
          </div>
        </div>

        <div class="score-summary">
          <h3>왜 이 커피가 나왔을까?</h3>
          <p class="reason-text">{{ result.reason }}</p>
        </div>

        <!-- 공유 버튼 -->
        <button class="share-btn" @click="shareResult">
          친구에게 공유하기
        </button>

        <!-- 다시하기 -->
        <button class="primary-btn" @click="resetTest">다시 하기</button>
      </div>

    </div>
  </div>
</template>

<script setup>
import { computed, reactive, ref } from 'vue'
import americanoImg from './assets/americano.png'
import latteImg from './assets/latte.png'
import vanillaImg from './assets/vanilla.png'
import coldbrewImg from './assets/coldbrew.png'

const step = ref('start')
const currentQuestionIndex = ref(0)

const questions = [
  {
    question: '평소 커피는 얼마나 진한 맛을 좋아하나요?',
    options: [
      { text: '진하고 깔끔한 맛이 좋다', type: 'americano' },
      { text: '부드러운 맛이 더 좋다', type: 'latte' },
      { text: '달콤하면 더 좋다', type: 'vanilla' },
      { text: '시원하고 산뜻한 느낌이 좋다', type: 'coldbrew' },
    ],
  },
  {
    question: '우유가 들어간 커피를 좋아하나요?',
    options: [
      { text: '거의 안 마신다', type: 'americano' },
      { text: '부드러워서 좋아한다', type: 'latte' },
      { text: '달달한 라떼류가 좋다', type: 'vanilla' },
      { text: '깔끔한 게 더 좋다', type: 'coldbrew' },
    ],
  },
  {
    question: '카페에서 가장 자주 끌리는 메뉴는?',
    options: [
      { text: '아메리카노', type: 'americano' },
      { text: '카페라떼', type: 'latte' },
      { text: '바닐라라떼', type: 'vanilla' },
      { text: '콜드브루', type: 'coldbrew' },
    ],
  },
  {
    question: '오늘 기분에 어울리는 단어는?',
    options: [
      { text: '집중', type: 'americano' },
      { text: '편안함', type: 'latte' },
      { text: '기분전환', type: 'vanilla' },
      { text: '상쾌함', type: 'coldbrew' },
    ],
  },
  {
    question: '커피를 마시는 이유는?',
    options: [
      { text: '각성', type: 'americano' },
      { text: '여유', type: 'latte' },
      { text: '달콤함', type: 'vanilla' },
      { text: '가벼움', type: 'coldbrew' },
    ],
  },
  {
    question: '디저트와 조합은?',
    options: [
      { text: '깔끔', type: 'americano' },
      { text: '부드러움', type: 'latte' },
      { text: '달콤함', type: 'vanilla' },
      { text: '가벼움', type: 'coldbrew' },
    ],
  },
  {
    question: '향 취향은?',
    options: [
      { text: '쌉싸름', type: 'americano' },
      { text: '고소함', type: 'latte' },
      { text: '달콤함', type: 'vanilla' },
      { text: '산뜻함', type: 'coldbrew' },
    ],
  },
  {
    question: '카페 분위기에서 중요한 건?',
    options: [
      { text: '집중', type: 'americano' },
      { text: '편안함', type: 'latte' },
      { text: '기분전환', type: 'vanilla' },
      { text: '상쾌함', type: 'coldbrew' },
    ],
  },
]

const scores = reactive({
  americano: 0,
  latte: 0,
  vanilla: 0,
  coldbrew: 0,
})

const resultMap = {
  americano: {
    title: '아메리카노',
    image: americanoImg,
    description: '깔끔하고 진한 커피를 선호하는 타입',
    style: '진함',
    temperature: '핫/아이스',
    time: '아침',
    dessert: '쿠키',
    reason: '집중형 성향',
  },
  latte: {
    title: '카페라떼',
    image: latteImg,
    description: '부드러운 커피를 좋아하는 타입',
    style: '부드러움',
    temperature: '핫',
    time: '오전',
    dessert: '케이크',
    reason: '안정형 성향',
  },
  vanilla: {
    title: '바닐라라떼',
    image: vanillaImg,
    description: '달콤한 커피를 좋아하는 타입',
    style: '달콤함',
    temperature: '아이스',
    time: '오후',
    dessert: '마카롱',
    reason: '감성형 성향',
  },
  coldbrew: {
    title: '콜드브루',
    image: coldbrewImg,
    description: '산뜻한 커피를 좋아하는 타입',
    style: '시원함',
    temperature: '아이스',
    time: '낮',
    dessert: '샌드위치',
    reason: '깔끔형 성향',
  },
}

const currentQuestion = computed(() => questions[currentQuestionIndex.value])
const progressPercent = computed(() =>
  ((currentQuestionIndex.value + 1) / questions.length) * 100
)

const resultType = computed(() => {
  const entries = Object.entries(scores)
  entries.sort((a, b) => b[1] - a[1])
  return entries[0][0]
})

const result = computed(() => resultMap[resultType.value])

function startTest() {
  step.value = 'question'
}

function selectOption(type) {
  scores[type]++
  if (currentQuestionIndex.value < questions.length - 1) {
    currentQuestionIndex.value++
  } else {
    step.value = 'result'
  }
}

function resetTest() {
  step.value = 'start'
  currentQuestionIndex.value = 0
  Object.keys(scores).forEach(k => scores[k] = 0)
}

function shareResult() {
  if (navigator.share) {
    navigator.share({
      title: '커피 테스트',
      text: `나는 ${result.value.title} 타입 ☕`,
      url: window.location.href,
    })
  } else {
    navigator.clipboard.writeText(window.location.href)
    alert('링크 복사 완료!')
  }
}
</script>