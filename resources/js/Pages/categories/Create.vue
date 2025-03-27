<script setup>
import { ref, computed } from "vue";
import { useForm, Head, router } from "@inertiajs/vue3";
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";

// Form initialization with validation
const form = useForm({
  name: "",
  description: "",
});

// Error tracking
const errors = ref({
  name: '',
  description: '',
});

// Validation function
const validateForm = () => {
  let isValid = true;
  
  // Clear previous errors
  errors.value.name = '';
  errors.value.description = '';
  
  // Name validation
  if (!form.name.trim()) {
    errors.value.name = 'Category name is required';
    isValid = false;
  } else if (form.name.trim().length < 2) {
    errors.value.name = 'Category name must be at least 2 characters';
    isValid = false;
  } else if (form.name.trim().length > 50) {
    errors.value.name = 'Category name must be less than 50 characters';
    isValid = false;
  }
  
  // Optional description validation
  if (form.description && form.description.trim().length > 200) {
    errors.value.description = 'Description must be less than 200 characters';
    isValid = false;
  }
  
  return isValid;
};

// Form submission function
const submit = () => {
  // Validate form first
  if (!validateForm()) {
    return;
  }
  
  form.post(route("categories.store"), {
    onSuccess: () => {
      form.reset();
      router.visit("/categorie", {
        replace: true,
        preserveState: false,
        preserveScroll: false,
      });
    },
    onError: (serverErrors) => {
      // Handle server-side validation errors
      if (serverErrors.name) {
        errors.value.name = serverErrors.name;
      }
      if (serverErrors.description) {
        errors.value.description = serverErrors.description;
      }
    }
  });
};

// Computed property to check form validity
const isFormValid = computed(() => {
  return form.name.trim().length >= 2 && form.name.trim().length <= 50;
});

// Character count for description
const descriptionLength = computed(() => form.description.length);
</script>

<template>
  <Head title="Create New Category" />
  <AuthenticatedLayout>
    <template #header>
      <div class="flex items-center justify-between">
        <h2 class="text-2xl font-bold text-black dark:text-black flex items-center">
          <span class="mr-3">➕</span> Create New Category
        </h2>
      </div>
    </template>

    <div class="container mx-auto px-4 py-6">
      <div class="max-w-xl mx-auto">
        <form 
          @submit.prevent="submit" 
          class="bg-white dark:bg-gray-800 shadow-lg rounded-xl p-8 space-y-6 border border-gray-100 dark:border-gray-700"
        >
          <div class="space-y-4">
            <div class="space-y-1">
              <label 
                for="name" 
                class="block text-sm font-medium text-gray-700 dark:text-gray-300"
              >
                Category Name
              </label>
              <div class="relative">
                <input
                  id="name"
                  v-model="form.name"
                  type="text"
                  autocomplete="off"
                  class="w-full px-4 py-2 border rounded-lg focus:outline-none 
                         transition-all duration-300
                         ${errors.name ? 'border-red-500 focus:ring-2 focus:ring-red-500' : 'border-gray-300 focus:ring-2 focus:ring-blue-500'}
                         dark:bg-gray-700 dark:text-white"
                  :class="{ 'border-red-500': errors.name }"
                />
                <div 
                  v-if="errors.name" 
                  class="absolute inset-y-0 right-0 pr-3 flex items-center text-red-500"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z" clip-rule="evenodd" />
                  </svg>
                </div>
              </div>
              <p 
                v-if="errors.name" 
                class="mt-1 text-sm text-red-600 dark:text-red-400"
              >
                {{ errors.name }}
              </p>
            </div>
          </div>
          <div class="flex justify-end space-x-4">
            <button
              type="button"
              @click="router.visit('/categorie')"
              class="px-4 py-2 text-gray-600 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700 rounded-lg transition-colors"
            >
              Cancel
            </button>
            <button
              type="submit"
              :disabled="!isFormValid || form.processing"
              class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 
                     focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2
                     dark:bg-blue-500 dark:hover:bg-blue-600
                     transition-colors duration-300
                     disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {{ form.processing ? 'Creating...' : 'Create Category' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<style scoped>
input:focus, textarea:focus {
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
}
</style>