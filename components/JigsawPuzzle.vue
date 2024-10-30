<template>
  <div class="controls" ref="controlsRef">
    <div v-if="showSolveCtrl" class="btn-icon" @click=solvePuzzle>
      <IconBrushSolid></IconBrushSolid>
    </div>

    <div v-if="showMixCtrl" class="btn-icon" @click=mixPuzzle>
      <IconVialSolid></IconVialSolid>
    </div>

    <div v-if="showEdgePiecesCtrl" :class="{ 'active': puzzleViewMode === 'BorderPieces' }" class="btn-icon"
      @click=togglePuzzleViewMode>
      <IconPuzzleSolid></IconPuzzleSolid>
    </div>

    <div :class="{ 'active': puzzleViewImageModalOpen }" class="btn-icon" @click="viewPuzzleViewImageModal">
      <IconImageSolid></IconImageSolid>
    </div>
    <Modal :isOpen="puzzleViewImageModalOpen" :imageSrc="imageUrl" @modal-close="hidePuzzleViewImageModal" />

    <div v-if="showImageOnCanvasCtrl" :class="{ 'active': puzzleShowImageOnCanvas }" class="btn-icon"
      @click=togglePuzzleShowImageOnCanvas>
      <IconEyeSlashedSolid v-if="!puzzleShowImageOnCanvas"></IconEyeSlashedSolid>
      <IconEyeSolid v-if="puzzleShowImageOnCanvas"></IconEyeSolid>
    </div>

    <div v-if="showHintsCtrl" :class="{ 'active': puzzleShowHints }" class="btn-icon" @click=togglePuzzleHints>
      <IconInfoSolid></IconInfoSolid>
    </div>
  </div>

  <canvas ref="canvasRef" :width="canvasWidth" :height="canvasHeight"></canvas>
</template>

<script setup>
import Puzzle from "~/assets/lib/puzzle.js";

import IconBrushSolid from "~/assets/icons/brush-solid.svg";
import IconVialSolid from "~/assets/icons/vial-solid.svg";
import IconPuzzleSolid from "~/assets/icons/puzzle-solid.svg";
import IconImageSolid from "~/assets/icons/image-solid.svg";
import IconEyeSolid from "~/assets/icons/eye-solid.svg";
import IconEyeSlashedSolid from "~/assets/icons/eye-slashed-solid.svg";
import IconInfoSolid from "~/assets/icons/information-solid.svg";

import { ref, onMounted } from 'vue';

import ModalComponent from "./Modal.vue";

const controlsRef = ref(null);
const canvasRef = ref(null);

const puzzle = ref(null);
const canvasWidth = ref(0);
const canvasHeight = ref(0);
const puzzleViewMode = ref('All');
const puzzleViewImageModalOpen = ref(false);
const puzzleShowImageOnCanvas = ref(false);
const puzzleShowHints = ref(false);

const props = defineProps({
  parentContainerRef: {
    type: Object,
    required: true,
  },
  imageUrl: {
    type: String,
    required: true,
  },
  rows: {
    type: Number,
    default: 6,
  },
  columns: {
    type: Number,
    default: 6,
  },
  maxImageWidth: {
    type: Number,
    default: 95,
  },
  maxImageHeight: {
    type: Number,
    default: 70,
  },
  animationDuration: {
    type: Number,
    default: 250,
  },

  pieceScroller: {
    type: Boolean,
    default: false,
  },
  showSolveCtrl: {
    type: Boolean,
    default: true,
  },
  showMixCtrl: {
    type: Boolean,
    default: true,
  },
  showEntireImageCtrl: {
    type: Boolean,
    default: true,
  },
  showEdgePiecesCtrl: {
    type: Boolean,
    default: true,
  },
  showImageOnCanvasCtrl: {
    type: Boolean,
    default: true,
  },
  showHintsCtrl: {
    type: Boolean,
    default: true,
  },
});

const solvePuzzle = () => {
  puzzle.value.solve();
};

const mixPuzzle = () => {
  puzzle.value.mixPuzzle();
};

const togglePuzzleViewMode = () => {
  if (puzzleViewMode.value === 'All') {
    puzzleViewMode.value = 'BorderPieces'
  } else {
    puzzleViewMode.value = 'All'
  }
  puzzle.value.viewMode = puzzleViewMode.value;
  if (props.pieceScroller) {
    puzzle.value.arrangePiecesInScroller();
  }
}

const viewPuzzleViewImageModal = () => {
  puzzleViewImageModalOpen.value = true;
};
const hidePuzzleViewImageModal = () => {
  puzzleViewImageModalOpen.value = false;
};

const togglePuzzleShowImageOnCanvas = () => {
  puzzleShowImageOnCanvas.value = !puzzleShowImageOnCanvas.value
  puzzle.value.toggleImageOnCanvas(puzzleShowImageOnCanvas.value);
};

const togglePuzzleHints = () => {
  puzzleShowHints.value = !puzzleShowHints.value
  puzzle.value.toggleHints(puzzleShowHints.value);
};

const initializePuzzle = () => {
  canvasWidth.value = props.parentContainerRef.offsetWidth;

  const styles = window.getComputedStyle(controlsRef.value);
  const margin =
    parseFloat(styles["marginTop"]) + parseFloat(styles["marginBottom"]);
  const controlsHeight = Math.ceil(controlsRef.value.offsetHeight + margin);
  canvasHeight.value = props.parentContainerRef.offsetHeight - controlsHeight;

  const img = new Image();
  img.src = props.imageUrl;
  img.onload = () => {
    puzzle.value = new Puzzle({
      canvas: canvasRef.value,
      image: img,
      rows: props.rows,
      columns: props.columns,
      maxImageHeight: props.maxImageHeight,
      maxImageWidth: props.maxImageWidth,
      animationDuration: props.animationDuration,
      pieceScroller: props.pieceScroller,
      showImageOnCanvas: puzzleShowImageOnCanvas.value,
      hintsEnabled: puzzleShowHints.value,
      canvasWidth: canvasWidth.value,
      canvasHeight: canvasHeight.value,
      scaleMultiplier: 1.0,
    });

    puzzle.value.generatePieces();
    if (props.pieceScroller) {
      puzzle.value.arrangePiecesInScroller();
    } else {
      puzzle.value.randomizePieces();
    }
    requestAnimationFrame(puzzle.value.draw.bind(puzzle.value));
  };
}

onMounted(() => {
  initializePuzzle();
});
</script>

<style scoped>
canvas {
  display: block;
  width: 100%;
}

.controls {
  width: 70%;
  height: 50px;
  float: right;
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 8px;
  margin: 16px 8px;
  border-radius: 50px;
  background-color: #fff;
  box-shadow: 0 0 16px 2px rgba(0, 0, 0, .15);
}

.controls .btn-icon {
  width: 36px;
  height: 36px;
  margin: 0 2px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  vertical-align: middle;
  background-color: transparent;
  cursor: pointer;
}

.btn-icon svg {
  width: 18px;
  fill: green;
  vertical-align: middle;
}

.btn-icon:hover,
.btn-icon.active {
  background-color: #d0d0d0;
}
</style>