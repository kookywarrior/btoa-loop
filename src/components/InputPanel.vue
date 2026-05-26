<script setup lang="ts">
interface Props {
  inputText: string
  numberCount: number | string | null
  needConsoleLog: boolean
  errors?: Record<string, string>
}

withDefaults(defineProps<Props>(), {
  errors: () => ({})
})

defineEmits<{
  'update:inputText': [value: string]
  'update:numberCount': [value: string]
  'update:needConsoleLog': [value: boolean]
  transform: []
}>()
</script>

<template>
  <div class="flex flex-col bg-[#fbf9f5] border border-[#dfd8c8] rounded-xl p-5 shadow-sm space-y-5">

    <div class="space-y-4">
      <div>
        <label for="numberCount" class="block text-sm font-bold text-[#2d2a26] mb-1.5">
          Transform Count
        </label>
        <input id="numberCount" type="number" :value="numberCount"
          @input="$emit('update:numberCount', ($event.target as HTMLInputElement).value)" placeholder="e.g. 1"
          class="w-full sm:max-w-xs bg-white border rounded-lg px-4 py-2.5 text-[#2d2a26] placeholder-[#9ca3af] focus:outline-none focus:ring-2 transition-all sm:text-sm"
          :class="errors.numberCount ? 'border-red-500 focus:ring-red-500/50' : 'border-[#dcd6c8] focus:ring-[#8c8273]/50 focus:border-[#8c8273]'" />
        <span v-if="errors.numberCount" class="text-xs text-red-600 font-medium block mt-1">
          {{ errors.numberCount }}
        </span>
      </div>

      <div class="flex items-center space-x-2.5">
        <div class="relative flex items-center">
          <input id="needConsoleLog" type="checkbox" :checked="needConsoleLog"
            @change="$emit('update:needConsoleLog', ($event.target as HTMLInputElement).checked)"
            class="w-5 h-5 appearance-none bg-white border border-[#dcd6c8] rounded checked:bg-[#2d2a26] checked:border-[#2d2a26] focus:outline-none focus:ring-2 focus:ring-[#8c8273]/50 transition-all cursor-pointer peer" />
          <svg
            class="absolute w-3 h-3 text-[#fbf9f5] pointer-events-none opacity-0 peer-checked:opacity-100 left-1 top-1"
            fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="3">
            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
          </svg>
        </div>
        <label for="needConsoleLog" class="text-sm font-bold text-[#2d2a26] cursor-pointer select-none">
          Need Console Log
        </label>
      </div>
    </div>

    <div class="flex flex-col">
      <div class="flex items-center justify-between mb-1.5">
        <label for="input" class="text-sm font-bold text-[#2d2a26]">
          Source Input
        </label>
        <span class="text-xs text-[#5c574f] font-medium">{{ inputText.length }} chars</span>
      </div>
      <textarea id="input" :value="inputText"
        @input="$emit('update:inputText', ($event.target as HTMLTextAreaElement).value)"
        placeholder="Type your text here..."
        class="w-full h-40 bg-white border rounded-lg p-4 text-[#2d2a26] placeholder-[#9ca3af] focus:outline-none focus:ring-2 resize-none sm:text-sm transition-all overflow-y-auto custom-scrollbar"
        :class="errors.inputText ? 'border-red-500 focus:ring-red-500/50' : 'border-[#dcd6c8] focus:ring-[#8c8273]/50 focus:border-[#8c8273]'"></textarea>
      <span v-if="errors.inputText" class="text-xs text-red-600 font-medium mt-1 block">
        {{ errors.inputText }}
      </span>
    </div>

    <div class="pt-2 border-t border-[#dfd8c8] flex justify-end">
      <button @click="$emit('transform')"
        class="bg-[#2d2a26] hover:bg-[#423f39] text-[#fbf9f5] px-6 py-2.5 rounded-lg text-sm font-bold tracking-wide transition-colors cursor-pointer shadow-sm active:scale-95">
        Transform
      </button>
    </div>

  </div>
</template>