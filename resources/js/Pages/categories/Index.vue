<script setup>
import { defineProps, ref, computed } from "vue";
import { Head, router } from "@inertiajs/vue3";
import axios from "axios";
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import { Trash2, Edit, PlusCircle } from 'lucide-vue-next';

const props = defineProps({
  categories: Array,
});

// Reactive list for updating after deletion
const categoriesList = ref(props.categories);
const searchQuery = ref('');

// Redirect to add page
const ajouterCategory = () => {
  router.visit("/categories/create");
};

// Function to modify a category
const modifierCategory = (id) => {
  router.visit(`/categories/${id}/edit`);
};

// Function to delete a category
function deleteCategory(id) {
  if (confirm("Are you sure you want to delete this category?")) {
    axios
      .delete(route("categories.destroy", { category: id }))
      .then(() => {
        categoriesList.value = categoriesList.value.filter(
          (category) => category.id !== id
        );
      })
      .catch((error) => {
        console.error("Error deleting category:", error);
      });
  }
}

// Computed property for filtered categories
const filteredCategories = computed(() => {
  return categoriesList.value.filter(category => 
    category.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});
</script>

<template>
  <Head title="Category Management" />
  <AuthenticatedLayout>
    <template #header>
      <div class="flex justify-between items-center">
        <h2 class="text-2xl font-bold text-black dark:text-black flex items-center">
          <span class="mr-3">📁</span> Category Management
        </h2>
      </div>
    </template>
    
    <div class="container mx-auto px-4 py-6">
      <div class="mb-6 flex justify-between items-center">
        <div class="relative w-full max-w-md">
          <input 
            v-model="searchQuery"
            type="text" 
            placeholder="Search categories..." 
            class="w-full px-4 py-2 border border-gray-300 rounded-lg pl-10 focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          >
          <svg xmlns="http://www.w3.org/2000/svg" class="absolute left-3 top-3 h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </div>
        
        <button 
          @click="ajouterCategory" 
          class="btn-primary flex items-center space-x-2 px-4 py-2 rounded-lg"
        >
          <PlusCircle class="w-5 h-5" />
          <span>Add Category</span>
        </button>
      </div>

      <div class="bg-white dark:bg-gray-800 shadow-md rounded-lg overflow-hidden">
        <table class="w-full">
          <thead class="bg-gray-100 dark:bg-gray-700">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                Name
              </th>
              <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">
                Actions
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr 
              v-for="category in filteredCategories" 
              :key="category.id" 
              class="hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
            >
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm font-medium text-gray-900 dark:text-gray-200">
                  {{ category.name }}
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-right">
                <div class="flex justify-end space-x-3">
                  <button 
                    @click="modifierCategory(category.id)"
                    class="text-blue-600 hover:text-blue-900 dark:text-blue-400 dark:hover:text-blue-300 transition-colors"
                    title="Edit"
                  >
                    <Edit class="w-5 h-5" />
                  </button>
                  <button 
                    @click="deleteCategory(category.id)"
                    class="text-red-600 hover:text-red-900 dark:text-red-400 dark:hover:text-red-300 transition-colors"
                    title="Delete"
                  >
                    <Trash2 class="w-5 h-5" />
                  </button>
                </div>
              </td>
            </tr>
            <tr v-if="filteredCategories.length === 0">
              <td colspan="2" class="text-center py-4 text-gray-500 dark:text-gray-400">
                No categories found
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<style scoped>
/* Tailwind CSS is used for most styling, but we can add some custom styles if needed */
.btn-primary {
  @apply bg-blue-500 text-white hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 dark:bg-blue-600 dark:hover:bg-blue-700;
}
</style>