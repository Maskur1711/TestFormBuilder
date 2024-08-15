<template>
  <transition name="fade">
    <div
      v-if="showModal"
      class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50"
      @click.self="closeModal"
    >
      <div class="bg-white p-4 rounded w-11/12 max-w-lg">
        <h2 class="text-xl font-semibold mb-4">Form Data</h2>
        <div v-for="(value, key) in formData" :key="key" class="mb-2">
          <strong>{{ key }}:</strong> <span>{{ value }}</span>
        </div>
        <button @click="closeModal" class="bg-gray-500 text-white px-4 py-2 rounded mt-4">
          Close
        </button>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    showModal: {
      type: Boolean,
      required: true,
    },
    formData: {
      type: Object,
      required: true,
    },
  },
  methods: {
    closeModal() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
/* Modal fade transition */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}
</style>
