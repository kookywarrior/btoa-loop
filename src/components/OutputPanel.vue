<script setup lang="ts">
import { ref } from 'vue'

interface Props {
  outputText?: string
}

const props = withDefaults(defineProps < Props > (), {
  outputText: ''
})

const copied = ref < boolean > (false)

const copyToClipboard = async (): Promise<void> => {
  if (!props.outputText) return
  try {
    await navigator.clipboard.writeText(props.outputText)
    copied.value = true
    setTimeout(() => { copied.value = false }, 2000)
  } catch (err) {
    console.error('Failed to copy text: ', err)
  }
}
</script>

<template>
  <div class="flex flex-col bg-[#fbf9f5] border border-[#dfd8c8] rounded-xl p-5 shadow-sm h-full">
    <div class="flex items-center justify-between mb-3">
      <span class="text-sm font-bold text-[#2d2a26]">
        Output
      </span>
      <button @click="copyToClipboard"
        class="text-xs bg-[#e8e2d3] hover:bg-[#dfd8c8] text-[#2d2a26] px-3 py-1.5 rounded-md transition-colors font-bold active:scale-95 cursor-pointer"
        :disabled="!outputText" :class="{ 'opacity-50 cursor-not-allowed': !outputText }">
        {{ copied ? 'Copied!' : 'Copy' }}
      </button>
    </div>

    <div
      class="w-full h-[312px] bg-white border border-[#dfd8c8] rounded-lg p-4 sm:text-sm relative overflow-y-auto custom-scrollbar">
      <div v-if="outputText" class="text-[#2d2a26] break-all whitespace-pre-wrap font-mono">
        {{ outputText }}
      </div>
      <div v-else
        class="text-[#8c8273] italic select-none absolute inset-0 flex items-center justify-center pointer-events-none">
        Output will appear here...
      </div>
    </div>
  </div>
</template>