<template>
  <div class="container">
    <div class="drag-area" :class="{ 'is-drag-over': isDragOver }" @click="handleDragAreaClick"
      @dragenter="handleDragEnter" @dragleave="handleDragLeave" @dragover="handleDragOver" @drop="handleDrop"
      v-if="!imgSrc">
      <input type="file" style="display: none;" @click="handleInputClick" @change="handleInputChange" ref="input">
      <div>点击或拖拽图片到此处上传</div>
    </div>
    <img :src="imgSrc" alt="" v-else>
  </div>
  <button @click="handleReset">重置</button>
</template>

<script setup lang="ts">
import { ref } from "vue";

const imgSrc = ref<string>();
const isDragOver = ref<boolean>(false);
const input = ref<HTMLInputElement>();

const handleDragEnter = () => {
  isDragOver.value = true;
};

const handleDragLeave = () => {
  isDragOver.value = false;
};

const handleDragOver = (e: DragEvent) => {
  e.preventDefault();
};

const processFile = (file?: File | null) => {
  if (!file || !file.type.includes('image')) return;

  const fileReader = new FileReader();
  fileReader.addEventListener('load', function onLoad() {
    imgSrc.value = this.result as string || '';
  });
  fileReader.readAsDataURL(file);
}

const handleDrop = (e: DragEvent) => {
  e.preventDefault();
  isDragOver.value = false;
  const file = e.dataTransfer?.files?.[0];
  processFile(file);
};

const handleDragAreaClick = (e: Event) => {
  input.value?.click();
};

const handleInputClick = (e: Event) => {
  e.stopPropagation();
}

const handleInputChange = (e: Event) => {
  const file = (e.target as HTMLInputElement | null)?.files?.item(0);
  processFile(file);
};

const handleReset = () => {
  imgSrc.value = '';
}
</script>

<style scoped>
.container {
  width: 300px;
  height: 300px;
  position: relative;
}

.drag-area {
  display: flex;
  justify-content: center;
  align-items: center;
  box-sizing: border-box;
  width: 100%;
  height: 100%;
  border: 2px dashed rgb(0, 128, 255);
  color: rgb(0, 128, 255);
  font-size: 16px;
  user-select: none;
}

.drag-area:hover,
.drag-area.is-drag-over {
  border-color: red;
  color: red;
  cursor: pointer;
}

img {
  width: 100%;
  height: 100%;
}

button {
  margin-top: 16px;
}
</style>
