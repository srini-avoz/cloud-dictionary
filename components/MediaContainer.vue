<script setup lang="ts">
import { ref } from "vue"
const props = defineProps({
  mediaList: {
    type: Object,
    default: null
  }
})

const videoRef = ref()
const mediaDialog = ref()

const activeMedia = ref()
const onClickMedia = (url: string, mediaType: string) => {
  activeMedia.value = {
    url: url,
    type: mediaType
  }
  mediaDialog.value.showModal()
}

const onClickDialog = (e: MouseEvent) => {
  const dialogDimensions = mediaDialog.value.getBoundingClientRect()
  if (
    e.clientX < dialogDimensions.left ||
    e.clientX > dialogDimensions.right ||
    e.clientY < dialogDimensions.top ||
    e.clientY > dialogDimensions.bottom
  ) {
    if (activeMedia.value.type == 'video')
      videoRef.value.pause();
    mediaDialog.value.close()
  }
}
</script>

<template>
  <menu class="flex flex-wrap gap-2">
    <li 
      v-for="media in props.mediaList" 
      :key="media.url"
      class="italic rounded-md p-1" 
    >
      <section 
        class="bg-gray-800 hover:bg-gray-600 
        rounded-md p-2 cursor-pointer
        transition duration-300 ease-in-out hover:ease-in"
      >
        <img 
          v-if="media.mediaType == 'image' || !media.type"
          :src="media.url" 
          class="w-32 h-32 rounded-md"
          @click="onClickMedia(media.url, media.type)"
        >
        <video 
          v-if="media.type == 'video'"
          :src="media.url" 
          class="w-32 h-32 rounded-md"
          @click="onClickMedia(media.url, media.type)"
        />
      </section>
    </li>
  </menu>
  <dialog 
    id="custom-dialog"
    ref="mediaDialog" 
    class="rounded-lg grid items-center max-w-2xl"
    @click="onClickDialog" 
  >
    <img 
      v-if="activeMedia?.type == 'image'"
      :src="activeMedia.url" 
      class="max-w-screen max-h-screen"
    >
    <video 
      v-if="activeMedia?.type == 'video'"
      :key="activeMedia.url" 
      ref="videoRef"
      controls 
      autoplay
    >
      <source 
        :src="activeMedia.url" 
        type="video/mp4" 
      >
    </video>
  </dialog>
</template>

<style scoped>
  dialog {
    transition: opacity 0.5s ease-in-out;
  }
  dialog:not([open]) {
    pointer-events: none;
    opacity: 0;
  }
  dialog::backdrop {
    backdrop-filter: blur(1rem);
  }
</style>