<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// 狀態控制
const isCountdown = ref(false)
const showZhuyin = ref(false)
const isDarkMode = ref(false)

// 時間相關
const currentTime = ref('')
const countdownSeconds = ref(0)
const inputMinutes = ref(10) // 倒數預設 10 分鐘
let timer = null

// 監考資料
const examData = ref({
  time: '',
  subject: '',
  proctor: ''
})

// 更新時間邏輯
const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('zh-TW', { hour12: false })
  
  // 倒數計時進行中
  if (isCountdown.value && countdownSeconds.value > 0) {
    countdownSeconds.value--
  }
}

// 開始倒數
const startCountdown = () => {
  countdownSeconds.value = inputMinutes.value * 60
}

// 按鈕切換方法
const toggleTimeMode = () => {
  isCountdown.value = !isCountdown.value
}

const toggleZhuyin = () => {
  showZhuyin.value = !showZhuyin.value
}

const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value
}

// 計算倒數時間顯示格式 (分:秒)
const displayCountdown = computed(() => {
  const m = Math.floor(countdownSeconds.value / 60).toString().padStart(2, '0')
  const s = (countdownSeconds.value % 60).toString().padStart(2, '0')
  return `${m}:${s}`
})

// 生命週期掛載與卸載
onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(timer)
})
</script>

<template>
  <div :class="['app-wrapper', { 'dark-mode': isDarkMode, 'zhuyin-mode': showZhuyin }]">
    
    <!-- 頂部標題區 -->
    <header class="header">
      <a href="https://www.et.tku.edu.tw/" target="_blank" class="tku-btn">
        <ruby>淡<rt>ㄉㄢˋ</rt>江<rt>ㄐㄧㄤ</rt>教<rt>ㄐㄧㄠˋ</rt>科<rt>ㄎㄜ</rt></ruby>
      </a>
      <h1 class="title">
        <ruby>互<rt>ㄏㄨˋ</rt>動<rt>ㄉㄨㄥˋ</rt>程<rt>ㄔㄥˊ</rt>式<rt>ㄕˋ</rt>設<rt>ㄕㄜˋ</rt>計<rt>ㄐㄧˋ</rt></ruby>_413730598
      </h1>
    </header>

    <main class="main-content">
      <!-- 工具按鈕區 -->
      <div class="toolbar">
        <button @click="toggleTimeMode">
          {{ isCountdown ? '切換現在時間' : '切換倒數計時' }}
        </button>
        <button @click="toggleZhuyin">
          {{ showZhuyin ? '關閉注音符號' : '顯示注音符號' }}
        </button>
        <button @click="toggleDarkMode">
          切換{{ isDarkMode ? '白底黑字' : '黑底白字' }}
        </button>
      </div>

      <!-- 時間顯示區 -->
      <div class="time-display">
        <div v-if="!isCountdown" class="clock-mode">
          <h2><ruby>現<rt>ㄒㄧㄢˋ</rt>在<rt>ㄗㄞˋ</rt>時<rt>ㄕˊ</rt>間<rt>ㄐㄧㄢ</rt></ruby></h2>
          <div class="big-time">{{ currentTime }}</div>
        </div>
        
        <div v-else class="countdown-mode">
          <h2><ruby>倒<rt>ㄉㄠˋ</rt>數<rt>ㄕㄨˇ</rt>計<rt>ㄐㄧˋ</rt>時<rt>ㄕˊ</rt></ruby></h2>
          <div class="big-time">{{ displayCountdown }}</div>
          <div class="countdown-setup">
            <input type="number" v-model="inputMinutes" min="1" class="time-input" /> 
            <ruby>分<rt>ㄈㄣ</rt>鐘<rt>ㄓㄨㄥ</rt></ruby>
            <button @click="startCountdown">開始</button>
          </div>
        </div>
      </div>

      <!-- 表單與資料顯示區 -->
      <div class="exam-section">
        <div class="form-card">
          <h3><ruby>輸<rt>ㄕㄨ</rt>入<rt>ㄖㄨˋ</rt>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>資<rt>ㄗ</rt>料<rt>ㄌㄧㄠˋ</rt></ruby></h3>
          <div class="form-group">
            <label>考試時間：</label>
            <input type="text" v-model="examData.time" placeholder="例如：10:00 - 12:00" />
          </div>
          <div class="form-group">
            <label>考試科目：</label>
            <input type="text" v-model="examData.subject" placeholder="例如：互動程式設計" />
          </div>
          <div class="form-group">
            <label>監考老師：</label>
            <input type="text" v-model="examData.proctor" placeholder="請輸入老師姓名" />
          </div>
        </div>

        <!-- 顯示輸入的資料 -->
        <div class="info-card" v-if="examData.time || examData.subject || examData.proctor">
          <h3><ruby>螢<rt>ㄧㄥˊ</rt>幕<rt>ㄇㄨˋ</rt>顯<rt>ㄒㄧㄢˇ</rt>示<rt>ㄕˋ</rt>資<rt>ㄗ</rt>訊<rt>ㄒㄩㄣˋ</rt></ruby></h3>
          <p><strong>時間：</strong> {{ examData.time }}</p>
          <p><strong>科目：</strong> {{ examData.subject }}</p>
          <p><strong>監考老師：</strong> {{ examData.proctor }}</p>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
/* 全域外觀設定 (深淺色切換) */
.app-wrapper {
  min-height: 100vh;
  font-family: 'Helvetica Neue', Arial, '微軟正黑體', sans-serif;
  background-color: #ffffff;
  color: #333333;
  transition: background-color 0.3s, color 0.3s;
  padding: 20px;
  box-sizing: border-box;
}
.app-wrapper.dark-mode {
  background-color: #121212;
  color: #f5f5f5;
}

/* 注音符號顯示/隱藏控制 */
rt {
  display: none; /* 預設不顯示注音 */
  font-size: 0.5em;
}
.zhuyin-mode rt {
  display: block; /* 啟動注音模式時顯示 */
}
ruby {
  ruby-align: center;
}

/* 頂部排版 */
.header {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  padding-bottom: 20px;
  border-bottom: 2px solid #ccc;
  margin-bottom: 20px;
}
.tku-btn {
  position: absolute;
  left: 0;
  background-color: #0066cc;
  color: #fff;
  padding: 8px 16px;
  border-radius: 6px;
  text-decoration: none;
  font-weight: bold;
  transition: opacity 0.2s;
}
.tku-btn:hover { opacity: 0.8; }
.title {
  margin: 0;
  font-size: 1.8rem;
  text-align: center;
}

/* 行動裝置響應式 header 處理 */
@media (max-width: 600px) {
  .header {
    flex-direction: column;
    gap: 15px;
  }
  .tku-btn {
    position: static;
    width: 100%;
    text-align: center;
    box-sizing: border-box;
  }
  .title { font-size: 1.5rem; }
}

/* 工具列與按鈕 */
.toolbar {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  margin-bottom: 40px;
}
button {
  padding: 10px 15px;
  border: 1px solid #999;
  border-radius: 6px;
  background-color: #f0f0f0;
  color: #333;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.2s;
}
button:hover { background-color: #e0e0e0; }

.dark-mode button {
  background-color: #333;
  color: #fff;
  border-color: #666;
}
.dark-mode button:hover { background-color: #444; }

/* 時間顯示區 */
.time-display {
  text-align: center;
  margin-bottom: 40px;
}
.big-time {
  font-size: 4rem;
  font-weight: bold;
  margin: 10px 0;
  font-family: monospace;
}
.countdown-setup {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  margin-top: 15px;
}
.time-input {
  width: 60px;
  padding: 5px;
  font-size: 1.2rem;
  text-align: center;
}

/* 表單區塊 */
.exam-section {
  display: flex;
  flex-direction: column;
  gap: 20px;
  max-width: 600px;
  margin: 0 auto;
}
.form-card, .info-card {
  padding: 20px;
  border-radius: 8px;
  border: 1px solid #ddd;
  background-color: #f9f9f9;
}
.dark-mode .form-card, .dark-mode .info-card {
  background-color: #1e1e1e;
  border-color: #444;
}
.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 15px;
}
.form-group label {
  margin-bottom: 5px;
  font-weight: bold;
}
.form-group input {
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}
.dark-mode .form-group input {
  background-color: #333;
  color: #fff;
  border-color: #555;
}
.info-card p {
  font-size: 1.2rem;
  margin: 10px 0;
}
</style>
