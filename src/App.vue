<template>
  <div class="app">
    <!-- 배경(절대 위치로 화면 전체에 걸침) -->
    <div class="sky" aria-hidden="true">
      <div class="moon"></div>

      <!-- 등불 (달 주변에 균형있게) -->
      <div
          v-for="n in 3"
          :key="n"
          class="lantern"
          :style="lanternStyle(n)"
      ></div>
    </div>

    <!-- 중앙 인사말 & 입력 -->
    <div class="greeting" role="region" aria-label="한가위 인사">
      <h1>🌕 한가위 잘 보내세요 🌾</h1>
      <p>보름달처럼 마음이 가득 찬 따뜻한 명절 되세요.</p>

      <div class="controls">
        <button @click="fallingLeaves = !fallingLeaves" type="button">
          {{ fallingLeaves ? '낙엽 멈추기 🍂' : '낙엽 흩날리기 🍁' }}
        </button>
      </div>

      <div class="message-input" aria-label="덕담 입력">
        <input
            v-model="newMessage"
            type="text"
            placeholder="당신의 한가위 덕담을 남겨보세요 ✨"
            @keyup.enter="addMessage"
        />
        <button @click="addMessage" type="button">남기기</button>
      </div>
    </div>

    <!-- 덕담 메시지(화면 하단에서 떠오름) -->
    <div class="messages" aria-live="polite">
      <transition-group name="fade" tag="div">
        <div
            v-for="msg in messages"
            :key="msg.id"
            class="message"
            :style="{ top: msg.top, left: msg.left }"
        >
          💬 {{ msg.text }}
        </div>
      </transition-group>
    </div>

    <!-- 낙엽 (초기 위치는 setup에서 한 번만 생성) -->
    <div v-if="fallingLeaves" class="leaves" aria-hidden="true">
      <div
          v-for="(leaf, i) in leaves"
          :key="leaf.id"
          class="leaf"
          :style="{
          left: leaf.left,
          top: leaf.top,
          animationDelay: leaf.delay,
          animationDuration: leaf.duration,
          transform: 'rotate(' + leaf.rotate + 'deg)'
        }"
      >
        <!-- 단순한 원형 스타일로 처리 (이모지 대신 CSS 원) -->
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Message {
  id: number
  text: string
  top: string
  left: string
}

interface Leaf {
  id: number
  left: string
  top: string
  delay: string
  duration: string
  rotate: number
}

const fallingLeaves = ref(true)
const newMessage = ref('')
const messages = ref<Message[]>([])

/** 메시지 추가: 위치는 추가 시 고정해서 저장 */
const addMessage = () => {
  const text = newMessage.value.trim()
  if (!text) return

  // 화면 하단-중앙 범위에서 랜덤 위치 계산 (고정값으로 저장)
  const top = 68 + Math.random() * 12 // 68% ~ 80%
  const left = 20 + Math.random() * 60 // 20% ~ 80%
  messages.value.unshift({
    id: Date.now() + Math.floor(Math.random() * 1000),
    text,
    top: `${top}%`,
    left: `${left}%`
  })
  newMessage.value = ''
}

/** 등불 위치 스타일 (달 주변에 균형 배치) */
const lanternStyle = (n: number) => {
  const offsets = [-140, 0, 140] // px offsets from center
  const top = 18 + n * 4
  return {
    top: `${top}%`,
    left: `calc(50% + ${offsets[n - 1]}px)`,
    animationDelay: `${n * 0.4}s`
  }
}

/** 낙엽: setup에서 단 한 번만 생성 — 리렌더로 위치가 바뀌지 않음 */
const leafCount = 14
const leaves = Array.from({ length: leafCount }, (_, i) => {
  const left = Math.round(Math.random() * 100) // vw
  const top = Math.round(Math.random() * -100) // vh (위에서 시작)
  const delay = (Math.random() * 5).toFixed(2) + 's'
  const duration = (7 + Math.random() * 6).toFixed(2) + 's'
  const rotate = Math.floor(Math.random() * 360)
  return {
    id: 1000 + i,
    left: `${left}vw`,
    top: `${top}vh`,
    delay,
    duration,
    rotate
  } as Leaf
})
</script>

<!-- 전역 리셋 포함: 브라우저 기본 여백 차단 및 #app 보장 -->
<style>

html, body, #app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
}

/* 안전하게 스크롤 숨김(원하면 제거) */
body {
  overflow: hidden;
}
</style>

<style scoped>
/* .app을 뷰포트 전체에 고정해서 좌우/상하 오프셋 0 보장 */
.app {
  position: fixed;
  inset: 0; /* top:0; right:0; bottom:0; left:0; */
  width: 100%;
  height: 100%;
  overflow: hidden;
  font-family: 'Pretendard', 'Noto Sans KR', sans-serif;
  color: #fffce8;
  background: linear-gradient(to top, #3b1d1d 0%, #5e3b2e 30%, #9a6c3f 100%);
  /* 완전 중앙 배치 */
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 하늘(절대) : 화면 전체 기준으로 배치 */
.sky {
  position: absolute;
  inset: 0;
  overflow: hidden;
  pointer-events: none;
}

/* 보름달 */
.moon {
  position: absolute;
  top: 8%;
  left: 50%;
  transform: translateX(-50%);
  width: 160px;
  height: 160px;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, #fffbe1, #f2d98d, #e5c467);
  box-shadow: 0 0 60px 20px #f6e7a2;
  z-index: 2;
}

/* 등불 (달 주변) */
.lantern {
  position: absolute;
  width: 40px;
  height: 50px;
  background: linear-gradient(to bottom, #ffb347, #ff7b00);
  border-radius: 6px;
  animation: floatLantern 5s ease-in-out infinite alternate;
  box-shadow: 0 0 10px 2px rgba(255, 180, 71, 0.8);
  z-index: 1;
}
@keyframes floatLantern {
  0% { transform: translateY(0); opacity: 0.8; }
  100% { transform: translateY(-10px); opacity: 1; }
}

/* 중앙 인사말(정확히 중앙) */
.greeting {
  position: relative; /* 부모가 이미 중앙 정렬이라 relative로 두면 중앙에 딱 위치 */
  z-index: 10;
  text-align: center;
  width: 100%;
  max-width: 720px;
  padding: 24px;
  box-sizing: border-box;
}
.greeting h1 {
  font-size: 2.1rem;
  margin: 0 0 8px 0;
  text-shadow: 0 0 18px rgba(255, 240, 160, 0.85);
}
.greeting p {
  margin: 0 0 16px 0;
  color: #fff8e1;
}

/* 버튼 / 입력 컨트롤 */
.controls {
  margin-bottom: 12px;
}
.controls button {
  background: rgba(255, 223, 128, 0.9);
  border: none;
  border-radius: 18px;
  padding: 0.5rem 1rem;
  color: #5c3b1f;
  cursor: pointer;
}
.message-input {
  display: flex;
  justify-content: center;
  gap: 8px;
  margin-top: 8px;
}
.message-input input {
  width: 300px;
  max-width: 60%;
  padding: 10px 12px;
  border-radius: 20px;
  border: none;
  outline: none;
  font-size: 1rem;
}
.message-input button {
  border-radius: 12px;
  padding: 8px 12px;
  background: #ffe28a;
  border: none;
  cursor: pointer;
}

/* 덕담 메시지(절대 위치로 고정) */
.messages {
  position: absolute;
  bottom: 8%;
  left: 0;
  width: 100%;
  pointer-events: none;
  z-index: 11;
}
.message {
  position: absolute;
  transform: translate(-50%, 0); /* left에 따라 중심 맞춤 */
  background: rgba(255, 244, 203, 0.95);
  color: #5c3b1f;
  border-radius: 14px;
  padding: 8px 12px;
  font-size: 0.95rem;
  box-shadow: 0 6px 18px rgba(0,0,0,0.12);
  animation: floatMessage 10s ease-in-out forwards;
}
@keyframes floatMessage {
  0% { transform: translate(-50%, 0); opacity: 0.95; }
  100% { transform: translate(-50%, -120px); opacity: 0; }
}

/* 낙엽 (한 번 생성된 좌표 유지) */
.leaves {
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 3;
}
.leaf {
  position: absolute;
  width: 18px;
  height: 18px;
  border-radius: 50% 50% 50% 0;
  background: radial-gradient(circle, #e69b3a 0%, #c6711e 60%);
  opacity: 0.9;
  box-shadow: 0 6px 18px rgba(0,0,0,0.12);
  animation-name: fall;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
}
@keyframes fall {
  0% { transform: translateY(0) rotate(0deg); opacity: 1; }
  100% { transform: translateY(120vh) rotate(360deg); opacity: 0; }
}

/* 등장/퇴장 fade */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.9s;
}
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>
