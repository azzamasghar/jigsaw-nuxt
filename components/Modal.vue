<script setup>
import { defineProps, defineEmits, ref } from "vue";

const props = defineProps({
  isOpen: Boolean,
  imageSrc: String
});

const emit = defineEmits(["modal-close"]);

const target = ref(null)
</script>

<template>
  <div v-if="isOpen" className="modal">
    <div ref="target">
      <span class="close" @click.stop="emit('modal-close')">&times;</span>
      <img class="modal-content" :src=imageSrc alt="Puzzle Full View" />
    </div>
  </div>
</template>

<style scoped>
/* The Modal (background) */
.modal {
  display: block;
  position: absolute;
  z-index: 1;
  right: 0;
  top: 60px;
  width: 70%;
  padding: 5px;
  background-color: #fff;
  border: 1px solid #dcd9d9;
}

/* Modal Content (the image) */
.modal-content {
  margin: auto;
  width: 100%;
  max-width: 700px;
}

/* The Close Button */
.close {
  position: absolute;
  top: 0px;
  right: 0px;
  color: #a29f9f;
  font-size: 24px;
  font-weight: bold;
  z-index: 100;
  background: #fff;
  padding: 5px;
  border-bottom-left-radius: 10px;
}

.close:hover,
.close:focus {
  color: #8c8a8a;
  text-decoration: none;
  cursor: pointer;
}
</style>