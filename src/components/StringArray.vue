<script setup lang="ts">
import { computed, defineProps } from 'vue'

const props = defineProps<{ modelValue: Array<string> }>()
const emit = defineEmits(['update:modelValue'])

const model = computed({
  get: () => props.modelValue?.join('\n') || '',
  set: (newValue) => {
    console.debug('new value', newValue.split('\n'))
    emit('update:modelValue', newValue.split('\n'))
  },
})
</script>

<template>
  <textarea v-model="model" />
</template>
