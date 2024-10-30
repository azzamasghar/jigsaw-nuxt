<template>
  <div className="App">
    <div ref="parentContainerRef" class="puzzle-container">
      <JigsawPuzzle v-if="isRefReady" :parentContainerRef="parentContainerRef" :imageUrl="PuzzleImage" :rows="6"
        :columns="6" :pieceScroller="true" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

import PuzzleImage from '~/assets/image.png';

const parentContainerRef = ref(null);
const isRefReady = ref(false);

onMounted(async () => {
  await nextTick();
  isRefReady.value = true;
})
</script>

<style>
* {
  box-sizing: border-box;
  user-select: none !important;
  font-family: 'Poppins', sans-serif;
}

body,
html {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  overflow: auto;
  background-color: #f0f0f0;
}

body {
  display: flex;
  justify-content: center;
  align-items: center;
}

.App {
  text-align: center;
}

.puzzle-container {
  width: 100vw;
  height: 100vh;
}
</style>