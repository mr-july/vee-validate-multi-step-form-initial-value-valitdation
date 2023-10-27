<script setup lang="ts">
import { Field, ErrorMessage, useField } from 'vee-validate'

const props = defineProps<{ isActive: boolean }>()
const { value: passwordValue } = useField<string>('password')
</script>

<template>
  <fieldset v-if="isActive">
    <label for="password">Password</label>
    <input v-model="passwordValue" type="password" id="password" />
    <ErrorMessage name="password" />

    <label for="confirmation">Confirm Password</label>
    <Field name="confirmPassword" type="password" id="confirmation" />
    <ErrorMessage name="confirmPassword" />
  </fieldset>
</template>
