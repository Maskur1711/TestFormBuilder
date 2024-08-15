<template>
  <div class="max-w-lg mx-auto p-4 border mt-[8rem]">
    <!-- Step Indicators -->
    <div class="flex justify-between items-center mb-4">
      <div v-for="step in steps" :key="step.step" class="flex-1 text-center">
        <div
          :class="[
            'rounded-full w-[80px] h-[80px] flex items-center justify-center mx-auto',
            currentStep === step.step
              ? 'bg-orange-500 text-white'
              : 'bg-gray-200 text-gray-500',
          ]"
        >
          <p class="text-xl font-semibold">{{ step.step }}</p>
        </div>
        <p :class="['mt-2', currentStep === step.step ? 'font-bold' : 'text-gray-500']">
          {{ step.title }}
        </p>
      </div>
    </div>

    <!-- Form Content -->
    <div v-if="currentStepContent">
      <h2 class="text-xl font-semibold mb-2">{{ currentStepContent.title }}</h2>
      <p class="mb-4">{{ currentStepContent.description }}</p>

      <div v-for="field in currentStepContent.fields" :key="field.label" class="mb-4">
        <label class="block mb-2 font-medium">{{ field.label }}</label>

        <!-- Textfield Input -->
        <div v-if="field.type === 'textfield'">
          <input
            v-model="formData[field.label]"
            type="text"
            :placeholder="field.placeholder"
            :class="[
              'border rounded w-full p-2',
              validationErrors[field.label] ? 'border-red-500' : '',
            ]"
          />
          <p v-if="validationErrors[field.label]" class="text-red-500 text-sm mt-1">
            {{ validationErrors[field.label] }}
          </p>
        </div>

        <!-- Radio Input -->
        <div v-if="field.type === 'radio'">
          <div
            v-for="option in field.options"
            :key="option.value"
            class="flex items-center mb-2"
          >
            <input
              v-model="formData[field.label]"
              type="radio"
              :value="option.value"
              :name="field.label"
              class="mr-2"
            />
            <label>{{ option.label }}</label>
          </div>
          <p v-if="validationErrors[field.label]" class="text-red-500 text-sm mt-1">
            {{ validationErrors[field.label] }}
          </p>
        </div>

        <!-- Textarea Input -->
        <div v-if="field.type === 'textarea'">
          <textarea
            v-model="formData[field.label]"
            :placeholder="field.placeholder"
            :class="[
              'border rounded w-full p-2',
              validationErrors[field.label] ? 'border-red-500' : '',
            ]"
          ></textarea>
          <p v-if="validationErrors[field.label]" class="text-red-500 text-sm mt-1">
            {{ validationErrors[field.label] }}
          </p>
        </div>

        <!-- Autocomplete Input -->
        <div v-if="field.type === 'autocomplete'">
          <input
            v-model="formData[field.label]"
            type="text"
            :placeholder="field.placeholder"
            list="titles"
            :class="[
              'border rounded w-full p-2',
              validationErrors[field.label] ? 'border-red-500' : '',
            ]"
          />
          <datalist id="titles">
            <option v-for="option in field.options" :key="option">{{ option }}</option>
          </datalist>
          <p v-if="validationErrors[field.label]" class="text-red-500 text-sm mt-1">
            {{ validationErrors[field.label] }}
          </p>
        </div>
      </div>

      <!-- Navigation Buttons -->
      <div class="flex justify-between mt-4">
        <button
          @click="prevStep"
          :disabled="currentStep === 1"
          class="bg-gray-500 text-white px-4 py-2 rounded"
        >
          Previous
        </button>
        <button
          v-if="currentStep < steps.length"
          @click="nextStep"
          :disabled="currentStep === steps.length"
          class="bg-blue-500 text-white px-4 py-2 rounded"
        >
          Next
        </button>
        <!-- Finish Button -->
        <button
          v-if="currentStep === steps.length"
          @click="showModal = true"
          class="bg-green-500 text-white px-4 py-2 rounded"
        >
          Finish
        </button>
      </div>
    </div>

    <!-- Modal -->
    <FormModal :showModal="showModal" :formData="formData" @close="showModal = false" />
  </div>
</template>

<script>
import formSteps from "../Data/formSteps.json";
import FormModal from "./FormModal.vue";

export default {
  components: {
    FormModal,
  },
  data() {
    return {
      steps: formSteps,
      currentStep: 1,
      formData: {},
      validationErrors: {}, // To hold validation errors
      showModal: false, // To control modal visibility
    };
  },
  computed: {
    currentStepContent() {
      return this.steps.find((step) => step.step === this.currentStep);
    },
  },
  methods: {
    validateStep() {
      const currentFields = this.currentStepContent.fields;
      const errors = {};
      currentFields.forEach((field) => {
        if (field.required && !this.formData[field.label]) {
          errors[field.label] = `${field.label} is required.`;
        }
      });
      this.validationErrors = errors;
      return Object.keys(errors).length === 0;
    },
    nextStep() {
      if (this.validateStep()) {
        if (this.currentStep < this.steps.length) {
          this.currentStep++;
        }
      }
    },
    prevStep() {
      if (this.currentStep > 1) {
        this.currentStep--;
      }
    },
  },
};
</script>

<style scoped>
input,
textarea {
  outline: none;
}

input:focus,
textarea:focus {
  border-color: #3b82f6;
  box-shadow: 0 0 0 1px #3b82f6;
}
</style>
