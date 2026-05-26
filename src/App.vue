<script setup lang="ts">
import { reactive, ref, watch } from 'vue'
import * as yup from 'yup'
import InputPanel from './components/InputPanel.vue'
import OutputPanel from './components/OutputPanel.vue'

// Custom Yup Number Utility
const yupNumber = (msg?: string) =>
  yup
    .number()
    .transform((value, originalValue) => {
      if (originalValue === '' || originalValue === null || originalValue === undefined) {
        return null
      }
      if (typeof originalValue === 'string') {
        const parsed = parseFloat(originalValue)
        return isNaN(parsed) ? undefined : parsed
      }
      return value
    })
    .nullable()
    .typeError(msg || 'Must be a number')

// State Typing
interface AppState {
  inputText: string
  numberCount: number | string | null
  needConsoleLog: boolean
}

const state = reactive<AppState>({
  inputText: '',
  numberCount: '',
  needConsoleLog: false
})

const errors = reactive<Record<string, string>>({
  inputText: '',
  numberCount: ''
})

const outputText = ref<string>('')

// Validation Schema
const schema = yup.object({
  inputText: yup.string().required('Input is required'),
  numberCount: yupNumber('Must be a valid number.')
    .required('Transform count is required')
    .min(1, 'Must be at least 1')
    .integer('Must be an integer'),
  needConsoleLog: yup.boolean()
})


// Run Validation
const validateForm = async (): Promise<boolean> => {
  errors.inputText = ''
  errors.numberCount = ''

  try {
    await schema.validate(state, { abortEarly: false })
    return true
  } catch (err: any) {
    if (err.inner) {
      err.inner.forEach((e: yup.ValidationError) => {
        if (e.path) errors[e.path] = e.message
      })
    }
    return false
  }
}

// Clear errors dynamically as the user types
watch(state, () => {
  if (errors.inputText || errors.numberCount) {
    validateForm()
  }
}, { deep: true })

// Action: Triggered by the Transform button
const handleTransform = async (): Promise<void> => {
  const isValid = await validateForm()
  if (!isValid) {
    outputText.value = ''
    return
  }

  const times = typeof state.numberCount === 'number' ? state.numberCount : parseInt(state.numberCount as string) || 1

  let output = state.inputText
  for (let i = 0; i < times; i++) {
    output = btoa(output)

    if (state.needConsoleLog) {
      output = `console.log(atob('${output}'))`
    }
  }

  outputText.value = output
}
</script>

<template>
  <div class="min-h-screen bg-[#f0ead6] text-[#2d2a26] flex flex-col items-center justify-center p-6 antialiased">
    <div class="w-full max-w-5xl z-10 space-y-8">

      <header class="mb-6 text-center md:text-left">
        <h1 class="text-3xl font-extrabold tracking-tight text-[#1a1816]" style="font-family: Georgia, serif;">
          BTOA Loop Transformer
        </h1>
        <p class="text-sm text-[#5c574f] mt-1 font-medium">Text will be transformed into Base 64 encoded strings by
          pre-defined count</p>
      </header>

      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <InputPanel v-model:input-text="state.inputText" v-model:number-count="state.numberCount"
          v-model:need-console-log="state.needConsoleLog" :errors="errors" @transform="handleTransform" />

        <OutputPanel :output-text="outputText" />
      </div>

    </div>
  </div>
</template>