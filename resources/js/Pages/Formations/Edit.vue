<template>
  <AuthenticatedLayout>
    <template #header>
      <div class="flex items-center space-x-4">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
        </svg>
        <h2 class="text-2xl font-bold text-black dark:text-black">
          Modifier la formation
        </h2>
      </div>
    </template>

    <div class="container mx-auto max-w-2xl px-4 py-8">
      <form @submit.prevent="updateFormation" class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-2xl border border-gray-200 dark:border-gray-700">
        <!-- Form Fields Container -->
        <div class="space-y-6">
          <!-- Titre -->
          <div>
            <label for="titre" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              Titre de la formation
            </label>
            <input 
              id="titre"
              v-model="form.titre" 
              type="text" 
              required
              class="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition duration-200 
              bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
            >
          </div>

          <!-- Prix -->
          <div>
            <label for="prix" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              Prix (€)
            </label>
            <input 
              id="prix"
              v-model="form.prix" 
              type="number" 
              min="0" 
              step="0.01"
              required
              class="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition duration-200
              bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
            >
          </div>

          <!-- Certification -->
          <div>
            <label for="estcertifiante" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              Certification
            </label>
            <select 
              id="estcertifiante"
              v-model="form.estcertifiante" 
              class="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition duration-200
              bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
            >
              <option :value="true">Oui</option>
              <option :value="false">Non</option>
            </select>
          </div>

          <!-- Catégorie -->
          <div>
            <label for="category" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              Catégorie
            </label>
            <select 
              id="category"
              v-model="form.category_id" 
              class="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition duration-200
              bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
            >
              <option v-for="category in categories" :key="category.id" :value="category.id">
                {{ category.name }}
              </option>
            </select>
          </div>

          <!-- Image Upload -->
          <div>
            <label for="image_formation" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              Image de formation
            </label>
            <div class="flex items-center space-x-4">
              <input 
                id="image_formation"
                type="file" 
                @change="handleImageUpload"
                accept="image/*"
                class="w-full file:mr-4 file:rounded-lg file:border-0 file:bg-blue-50 file:px-4 file:py-2 file:text-blue-700 hover:file:bg-blue-100"
              >
              <img 
                v-if="imagePreview" 
                :src="imagePreview" 
                alt="Preview" 
                class="w-20 h-20 object-cover rounded-lg"
              >
            </div>
          </div>
        </div>

        <!-- Action Buttons -->
        <div class="flex justify-between mt-8 space-x-4">
          <button 
            type="submit" 
            :disabled="form.processing"
            class="flex-1 px-6 py-3 bg-blue-600 text-white font-semibold rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition duration-200 
            disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center space-x-2"
          >
            <svg v-if="form.processing" class="animate-spin h-5 w-5" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <span>💾 Sauvegarder</span>
          </button>
          
          <Link 
            :href="`/formations/${formation.id}`"
            class="flex-1 px-6 py-3 bg-gray-200 dark:bg-gray-700 text-gray-700 dark:text-gray-300 font-semibold rounded-lg hover:bg-gray-300 dark:hover:bg-gray-600 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition duration-200 text-center"
          >
            Annuler
          </Link>
        </div>
      </form>
    </div>
  </AuthenticatedLayout>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from "vue";
import { useForm, router } from "@inertiajs/vue3";
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import axios from "axios";

interface Category {
  id: number;
  name: string;
}

interface Formation {
  id: number;
  titre: string;
  prix: number;
  estcertifiante: boolean;
  image_formation: string | null;
  category_id: number;
}

const categories = ref<Category[]>([]);
const imagePreview = ref<string | null>(null);

// Load categories on component mount
onMounted(async () => {
  try {
    const response = await axios.get('/categories');
    categories.value = response.data;
  } catch (error) {
    console.error("Erreur lors du chargement des catégories", error);
  }
});

const props = defineProps<{ formation: Formation }>();

const form = useForm({
  titre: props.formation.titre,
  prix: props.formation.prix,
  estcertifiante: props.formation.estcertifiante,
  category_id: props.formation.category_id,
  image_formation: null as File | null
});

const handleImageUpload = (event: Event) => {
  const file = (event.target as HTMLInputElement).files?.[0];
  if (file) {
    form.image_formation = file;
    imagePreview.value = URL.createObjectURL(file);
  }
};

const updateFormation = () => {
  router.post(`/formations/${props.formation.id}`, {
    _method: "PUT",
    titre: form.titre,
    prix: form.prix,
    estcertifiante: form.estcertifiante,
    category_id: form.category_id,
    image_formation: form.image_formation
  }, {
    onSuccess: () => {
      // Using a toast notification instead of an alert
      alert("Formation mise à jour avec succès !");
    }
  });
};
</script>