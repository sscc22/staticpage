<template>
  <div class="app">
    <!-- 하늘 배경 -->
    <div class="sky">
      <div class="moon"></div>
      <div class="lantern" v-for="n in 3" :key="n" :style="lanternStyle(n)"></div>
    </div>

    <!-- 중앙 인사말 -->
    <div class="greeting">
      <h1>🌕 한가위 잘 보내세요 🌾</h1>
      <p>보름달처럼 마음이 가득 찬 따뜻한 명절 되세요.</p>

      <div class="buttons">
        <button @click="fallingLeaves = !fallingLeaves">
          {{ fallingLeaves ? '낙엽 멈추기 🍂' : '낙엽 흩날리기 🍁' }}
        </button>
      </div>

      <!-- 덕담 입력 -->
      <div class="message-input">
        <input
            v-model="newMessage"
            type="text"
            placeholder="당신의 한가위 덕담을 남겨보세요 ✨"
            @keyup.enter="addMessage"
        />
        <button @click="addMessage">남기기</button>
      </div>
    </div>

    <!-- 덕담 메시지 목록 -->
    <transition-group name="fade" tag="div" class="messages">
      <div
          v-for="msg in messages"
          :key="msg.id"
          class="message"
          :style="{ top: msg.top, left: msg.left }"
      >
        💬 {{ msg.text }}
      </div>
    </transition-group>

    <!-- 낙엽 -->
    <div v-if="fallingLeaves" class="leaves">
      <div v-for="n in 15" :key="n" class="leaf" :style="leafStyle(n)"></div>
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


const fallingLeaves = ref(true)
const newMessage = ref('')
const messages = ref<Message[]>([])

const addMessage = () => {
  if (!newMessage.value.trim()) return
  const top = 70 + Math.random() * 10
  const left = 20 + Math.random() * 60
  messages.value.unshift({
    id: Date.now(),
    text: newMessage.value.trim(),
    top: `${top}%`,
    left: `${left}%`
  })
  newMessage.value = ''
}
const leafStyle = (n: number) => {
  const delay = Math.random() * 5
  const duration = 5 + Math.random() * 5
  const left = Math.random() * 100
  const size = 10 + Math.random() * 20
  const rotate = Math.random() * 360
  return {
    left: `${left}%`,
    animationDelay: `${delay}s`,
    animationDuration: `${duration}s`,
    width: `${size}px`,
    height: `${size}px`,
    transform: `rotate(${rotate}deg)`
  }
}

const lanternStyle = (n: number) => {
  const offsetX = (n - 2) * 100 // -100, 0, +100
  const top = 30 + n * 5
  const delay = n * 0.5
  return {
    top: `${top}%`,
    left: `calc(50% + ${offsetX}px)`,
    animationDelay: `${delay}s`
  }
}

const messageStyle = (index: number) => {
  const top = 70 + Math.random() * 10
  const left = 20 + Math.random() * 60
  const delay = index * 0.2
  return {
    top: `${top}%`,
    left: `${left}%`,
    animationDelay: `${delay}s`
  }
}
</script>

<style scoped>
html, body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  overflow: hidden; /* 혹시 스크롤 생기면 제거 */
}

.app {
  overflow: hidden;
  width: 100vw;
  height: 100vh;
  background: linear-gradient(to top, #3b1d1d 0%, #5e3b2e 30%, #9a6c3f 100%);
  position: relative;
  font-family: 'Pretendard', 'Noto Sans KR', sans-serif;
  color: #fffce8;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

/* 하늘 */
.sky {
  position: absolute;
  inset: 0;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  overflow: hidden;
}

/* 보름달 */
.moon {
  position: absolute;
  top: 10%;
  left: 50%;
  transform: translateX(-50%);
  width: 160px;
  height: 160px;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, #fffbe1, #f2d98d, #e5c467);
  box-shadow: 0 0 60px 20px #f6e7a2;
  z-index: 2;
}

/* 등불 */
.lantern {
  position: absolute;
  width: 40px;
  height: 50px;
  background: linear-gradient(to bottom, #ffb347, #ff7b00);
  border-radius: 5px;
  animation: floatLantern 5s ease-in-out infinite alternate;
  box-shadow: 0 0 10px 2px rgba(255, 180, 71, 0.8);
  z-index: 1;
}
.lantern::after {
  content: '';
  position: absolute;
  top: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 2px;
  height: 10px;
  background: rgba(255, 255, 255, 0.5);
}
@keyframes floatLantern {
  0% { transform: translateY(0); opacity: 0.8; }
  100% { transform: translateY(-10px); opacity: 1; }
}

/* 인사말 */
.greeting {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
  z-index: 10;
}
.greeting h1 {
  font-size: 2.2rem;
  margin-bottom: 1rem;
  text-shadow: 0 0 20px rgba(255, 240, 160, 0.8);
}
.greeting p {
  font-size: 1.1rem;
  margin-bottom: 1rem;
  color: #fff8e1;
}
.buttons {
  margin-bottom: 1.5rem;
}
.greeting button {
  background: rgba(255, 223, 128, 0.8);
  border: none;
  border-radius: 20px;
  padding: 0.7rem 1.2rem;
  font-size: 1rem;
  color: #5c3b1f;
  cursor: pointer;
  transition: background 0.3s;
}
.greeting button:hover {
  background: rgba(255, 200, 90, 1);
}

/* 덕담 입력 */
.message-input {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
  margin-top: 1rem;
}
.message-input input {
  width: 300px;
  padding: 0.6rem 1rem;
  border-radius: 20px;
  border: none;
  outline: none;
  font-size: 1rem;
}
.message-input button {
  background: #ffe28a;
  border: none;
  border-radius: 20px;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  color: #5c3b1f;
  cursor: pointer;
  transition: background 0.3s;
}
.message-input button:hover {
  background: #ffd35f;
}

/* 덕담 메시지 */
.messages {
  position: absolute;
  bottom: 10%;
  width: 100%;
  text-align: center;
  pointer-events: none;
}
.message {
  position: absolute;
  background: rgba(255, 244, 203, 0.9);
  color: #5c3b1f;
  border-radius: 15px;
  padding: 0.5rem 1rem;
  font-size: 0.95rem;
  opacity: 0.9;
  animation: floatMessage 10s ease-in-out forwards;
  box-shadow: 0 0 10px rgba(255, 230, 180, 0.6);
}
@keyframes floatMessage {
  0% { transform: translateY(0); opacity: 0.9; }
  100% { transform: translateY(-100px); opacity: 0; }
}

/* 낙엽 */
.leaves {
  position: absolute;
  inset: 0;
  overflow: hidden;
}
.leaf {
  position: absolute;
  top: -20px;
  background: radial-gradient(circle, #e69b3a 0%, #c6711e 60%);
  border-radius: 50% 50% 50% 0;
  opacity: 0.8;
  animation: fall 10s linear infinite;
}
@keyframes fall {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.9;
  }
  100% {
    transform: translateY(110vh) rotate(720deg);
    opacity: 0;
  }
}

/* 등장 효과 */
.fade-enter-active, .fade-leave-active {
  transition: opacity 1s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
