<template>
  <div class="app">
    <h1 class="title">Pomodoro Timer🍅</h1>

    <!-- 完了ポップアップ -->
    <div v-if="showPopup">
      ポモドーロ完了！
    </div>

    <!-- モード切替 -->
    <div class="modes">
      <button class="mode" :class="{active:mode == 'focus'}">集中</button>
      <button class="mode" :class="{active:mode == 'break'}">休憩</button>
    </div>

    <!-- タイマー表示 -->
    <div class="card">
      <!-- マスタッシュ構文 -->
      <p class="time">{{formatTime(remainingTime)}}</p>
      <p class="cycle"> サイクル：{{cycleCount(sumCycle)}}</p>
    </div>

    <!-- 操作ボタン -->
    <div class="buttons">
      <button class="btn" @click="startTimer">スタート</button>
      <button class="btn" @click="resetTimer">リセット</button>
      <button class="btn" @click="stopTimer">ストップ</button>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { ref } from 'vue';

  // タイマー表示
  const focusTime = 0.1*60;
  const breakTime = 0.05*60;

  const remainingTime = ref<number>(focusTime);  
  let timerId: number;
  const showPopup = ref(false);
  
  // 減らす
  function minusRemainingTime(){
    remainingTime.value--;
    if(remainingTime.value == 0){
      stopTimer();
      const shouldContinue = switchMode();
      if(shouldContinue){
        startTimer();
      }else{
        resetTimer();
        showPopup.value = true;
      }
    }
  }

  // 時間表示
  function formatTime(values: number){
    const minutes: string = Math.floor(values / 60).toString()
    const seconds: string = (values % 60).toString().padStart(2,'0')
    return minutes + ":" + seconds;
  }

  // サイクル表示
  const sumCycle = ref(2)
  const nowCycle = ref(1);

  function cycleCount(totalCycle: number){
    return nowCycle.value + "/" + sumCycle.value;
  }

  // スタート制御
  let isRunning: boolean = false;
  function startTimer(){
    if(!isRunning){
      timerId = setInterval(minusRemainingTime,1000);
      isRunning = true;
    }
    showPopup.value = false;
  }

  // リセット制御
  function resetTimer(){
    stopTimer();
    remainingTime.value = focusTime;
    mode.value = "focus";
    nowCycle.value = 1;
  }

  // ストップ制御
  function stopTimer(){
    clearInterval(timerId);
    isRunning = false;
  }

  // モード制御
  const mode = ref("focus");
  function switchMode(){
    if(mode.value == "focus"){
      mode.value = "break";
      remainingTime.value = breakTime;
      return true;
    }else{
      if(nowCycle.value < sumCycle.value){
        nowCycle.value++;
        mode.value = "focus";
        remainingTime.value = focusTime;
        return true;
      }else{
        return false;
      }
    }
  }
</script>

<style scoped>
.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 24px;
  background: #1e1e2a;
  color: #f0f0f5;
  font-family: sans-serif;
}

.title {
  font-size: 20px;
  font-weight: 600;
  color: #f0f0f5;
}

/* モード切替 */
.modes {
  display: flex;
  gap: 8px;
}

.mode {
  padding: 8px 20px;
  border: none;
  border-radius: 20px;
  background: #2e2e3e;
  color: #a0a0b0;
  font-size: 14px;
  cursor: pointer;
}

.mode.active {
  background: #ff6b6b;
  color: #fff;
}

/* タイマー表示 */
.card {
  width: 280px;
  padding: 40px 0;
  border-radius: 16px;
  background: #2e2e3e;
  text-align: center;
}

.time {
  margin: 10px;
  font-size: 64px;
  font-weight: 700;
}

.cycle {
  margin: 20px 0 0;
  font-size: 13px;
  color: #a0a0b0;
}

/* 操作ボタン */
.buttons {
  display: flex;
  gap: 12px;
}

.btn {
  padding: 12px 28px;
  border: 1px solid #4a4a5a;
  border-radius: 8px;
  background: transparent;
  color: #f0f0f5;
  font-size: 15px;
  cursor: pointer;
}

.btn:hover {
  background: #ff5252;
}

</style>