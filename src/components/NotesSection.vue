<script setup lang="ts">
import { ErrorMessage, useField } from "vee-validate";
import StringArray from "./StringArray.vue";

const props = defineProps<{ isActive: boolean }>();
const { value: notesValue, errors } = useField<Array<string>>("notes");
</script>

<template>
  <fieldset v-if="isActive">
    <label for="notes">Notes</label>
    <string-array v-model="notesValue" />
    <ErrorMessage name="notes" />
    <div v-if="errors.length">
      Errors:
      <ul>
        <li v-for="error in errors" :key="error" v-text="error" />
      </ul>
    </div>
  </fieldset>
</template>
