<script setup lang="ts">
import { ref, computed } from "vue";
import { Field, ErrorMessage, useForm } from "vee-validate";
import * as yup from "yup";

import { toTypedSchema } from "./yup";

import NotesSection from "./components/NotesSection.vue";
import PasswordSection from "./components/PasswordSection.vue";

const currentStep = ref(0);

// Each step should have its own validation schema
const schemas = [
  {
    name: yup.string().required(),
    email: yup.string().required().email(),
  },
  {
    notes: yup.array().of(yup.string().required()).required().min(1),
  },
  {
    password: yup.string().required().min(6),
    confirmPassword: yup
      .string()
      .required()
      .min(6)
      .oneOf([yup.ref("password")], "Passwords must match"),
  },
  {
    address: yup.string().required(),
    postalCode: yup
      .string()
      .required()
      .matches(/^[0-9]+$/, "Must be numeric"),
  },
  {
    terms: yup.bool().required().equals([true]),
  },
];

const currentSchema = computed(() => {
  return toTypedSchema(
    yup.object(
      schemas
        .slice(0, currentStep.value + 1)
        .reduce((akk, it) => ({ ...akk, ...it }), {}),
    ),
  );
});

const formContext = useForm({
  validationSchema: currentSchema,
  keepValuesOnUnmount: true,
  initialValues: {
    name: "user",
    email: "user@aol.com",
    notes: [],
    password: "",
  },
});

const { handleSubmit, values, errors } = formContext;

const nextStep = handleSubmit(() => {
  if (currentStep.value === schemas.length) {
    console.log("Done: ", JSON.stringify(values, null, 2));
    return;
  }

  currentStep.value++;
});

function prevStep() {
  if (currentStep.value <= 0) {
    return;
  }

  currentStep.value--;
}

// This is a workaround for the problem of validating array values.
// The `notes' field is initially an empty array (`[]`),
// but it is validated even though it is not touched
// (some more investigation is needed to be sure this is a bug
// in the "vee-validate" library).
// Therefore, the field should be explicitly reset to disable validation of the initial value.
// If the line is commented out, then the form error will contain an error.
// '"notes": "notes field must have at least 1 items"'
//formContext.resetField('notes')
</script>

<template>
  <div>
    <h1>vee-validate form wizard</h1>
    <p>
      This example showcases a simple multi-step form (form wizard), basically
      we use the `handleSubmit` function before moving to the next step, which
      allows us to validate the current step without having to submit the form.

      <br />
      <br />

      For this use-case you should pass `keepValues` to the form to let
      vee-validate keep the values across steps.
    </p>

    <details open>
      <summary>Form Values</summary>
      <pre v-text="formContext.values" />
    </details>
    <details open>
      <summary>Form Errors</summary>
      <pre v-text="formContext.errors" />
    </details>

    <form @submit.prevent="nextStep">
      <label for="name">Name</label>
      <Field name="name" id="name" />
      <ErrorMessage name="name" />

      <label for="email">Email</label>
      <Field name="email" id="email" type="email" />
      <ErrorMessage name="email" />

      <notes-section v-if="currentStep >= 1" :is-active="currentStep >= 1" />

      <password-section :is-active="currentStep >= 2" />

      <template v-show="currentStep === 3">
        <label for="address">Address</label>
        <Field as="textarea" name="address" id="address" />
        <ErrorMessage name="address" />

        <label for="postalCode">Postal Code</label>
        <Field name="postalCode" id="postalCode" />
        <ErrorMessage name="postalCode" />
      </template>

      <template v-show="currentStep === 4">
        <label for="terms">Agree to terms and conditions</label>
        <Field name="terms" type="checkbox" id="terms" :value="true" />
        <ErrorMessage name="terms" />
      </template>

      <button v-show="currentStep !== 0" type="button" @click="prevStep">
        Previous
      </button>

      <button v-show="currentStep !== 3" type="submit">Next</button>

      <button v-show="currentStep === 3" type="submit">Finish</button>
    </form>
  </div>
</template>

<style>
#app {
  font-family: Arial, Helvetica, sans-serif;
  max-width: 500px;
  padding-bottom: 100px;
}

input {
  display: block;
}

span {
  display: block;
  margin-bottom: 20px;
}

label {
  display: block;
  margin-top: 20px;
}

button {
  display: block;
  margin-top: 10px;
}

button[type="submit"] {
  margin-top: 10px;
}

form {
  padding: 20px;
  border: 1px solid black;
}

p {
  font-size: 14px;
}
</style>
