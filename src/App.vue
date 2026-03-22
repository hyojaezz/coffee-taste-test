<template>
  <div class="app">
    <div class="container">
      <div v-if="step === 'start'" class="card start-card">
        <h1>내 취향 커피 테스트</h1>
        <p class="sub-text">
          몇 가지 질문에 답하고
          나와 잘 맞는 커피를 찾아보세요.
        </p>
        <button class="primary-btn" @click="startTest">테스트 시작</button>
      </div>

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
          {{ currentQuestion.question }}
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

      <div v-else-if="step === 'result'" class="card result-card">
        <p class="result-label">추천 결과</p>
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
        </div>

        <div class="score-summary">
          <h3>취향 요약</h3>
          <ul>
            <li>진하고 깔끔한 취향: {{ scores.americano }}</li>
            <li>부드럽고 고소한 취향: {{ scores.latte }}</li>
            <li>달콤한 디저트 취향: {{ scores.vanilla }}</li>
            <li>산뜻하고 시원한 취향: {{ scores.coldbrew }}</li>
          </ul>
        </div>

        <button class="primary-btn" @click="resetTest">다시 하기</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, reactive, ref } from 'vue'

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
      { text: '상관없지만 깔끔한 게 더 좋다', type: 'coldbrew' },
    ],
  },
  {
    question: '카페에서 가장 자주 끌리는 메뉴는?',
    options: [
      { text: '아이스 아메리카노', type: 'americano' },
      { text: '카페라떼', type: 'latte' },
      { text: '바닐라라떼', type: 'vanilla' },
      { text: '콜드브루', type: 'coldbrew' },
    ],
  },
  {
    question: '오늘 기분에 가장 어울리는 한 단어는?',
    options: [
      { text: '집중', type: 'americano' },
      { text: '편안함', type: 'latte' },
      { text: '기분전환', type: 'vanilla' },
      { text: '상쾌함', type: 'coldbrew' },
    ],
  },
  {
    question: '커피를 마시는 가장 큰 이유는?',
    options: [
      { text: '정신을 깨우기 위해서', type: 'americano' },
      { text: '부드럽게 즐기고 싶어서', type: 'latte' },
      { text: '달콤한 한 잔이 필요해서', type: 'vanilla' },
      { text: '무겁지 않게 시원하게 마시고 싶어서', type: 'coldbrew' },
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
    description:
      '당신은 군더더기 없이 깔끔하고 진한 커피를 선호하는 타입입니다. 집중이 필요한 순간에 가장 잘 맞는 선택이에요.',
    style: '진하고 깔끔함',
    temperature: '아이스 또는 핫',
  },
  latte: {
    title: '카페라떼',
    description:
      '당신은 너무 강하지 않고 부드럽게 즐길 수 있는 커피를 좋아하는 타입입니다. 편안한 분위기와 잘 어울려요.',
    style: '부드럽고 고소함',
    temperature: '핫 추천',
  },
  vanilla: {
    title: '바닐라라떼',
    description:
      '당신은 커피에서도 달콤한 만족감을 중요하게 생각하는 타입입니다. 디저트 같은 한 잔이 잘 어울려요.',
    style: '달콤하고 부드러움',
    temperature: '아이스 추천',
  },
  coldbrew: {
    title: '콜드브루',
    description:
      '당신은 깔끔하면서도 산뜻한 느낌의 커피를 선호하는 타입입니다. 묵직함보다 시원한 밸런스를 좋아해요.',
    style: '산뜻하고 시원함',
    temperature: '아이스 추천',
  },
}

const currentQuestion = computed(() => questions[currentQuestionIndex.value])

const progressPercent = computed(() => {
  return ((currentQuestionIndex.value + 1) / questions.length) * 100
})

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
  scores[type] += 1

  if (currentQuestionIndex.value < questions.length - 1) {
    currentQuestionIndex.value += 1
  } else {
    step.value = 'result'
  }
}

function resetTest() {
  step.value = 'start'
  currentQuestionIndex.value = 0

  scores.americano = 0
  scores.latte = 0
  scores.vanilla = 0
  scores.coldbrew = 0
}
</script>