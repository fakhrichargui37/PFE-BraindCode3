<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import DeleteUserForm from './Partials/DeleteUserForm.vue';
import UpdatePasswordForm from './Partials/UpdatePasswordForm.vue';
import UpdateProfileInformationForm from './Partials/UpdateProfileInformationForm.vue';
import { Head } from '@inertiajs/vue3';
import { ref } from 'vue';

defineProps({
    mustVerifyEmail: {
        type: Boolean,
    },
    status: {
        type: String,
    },
});

const sections = [
    { 
        title: 'Profile Information', 
        description: 'Update your account\'s profile information and email address.',
        component: UpdateProfileInformationForm,
        icon: 'M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z'
    },
    { 
        title: 'Update Password', 
        description: 'Ensure your account is using a long, random password to stay secure.',
        component: UpdatePasswordForm,
        icon: 'M15 7a2 2 0 012 2m4 0a6 6 0 01-7.743 5.743L11 14l-4 4h-3v3H3v-3l4.257-4.257a6 6 0 017.411-7.516A5.957 5.957 0 0121 7z'
    },
    { 
        title: 'Delete Account', 
        description: 'Permanently delete your account and all associated data.',
        component: DeleteUserForm,
        icon: 'M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16'
    }
];
</script>

<template>
    <Head title="Profile" />

    <AuthenticatedLayout>
        <template #header>
            <div class="flex items-center space-x-4">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-gray-600 dark:text-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zm-4 7a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
                </svg>
                <h2 class="text-2xl font-bold text-black dark:text-black">
                    Profile Settings
                </h2>
            </div>
        </template>

        <div class="py-12">
            <div class="mx-auto max-w-4xl space-y-8 sm:px-6 lg:px-8">
                <div 
                    v-for="(section, index) in sections" 
                    :key="section.title"
                    class="bg-white dark:bg-gray-800 shadow-lg rounded-xl overflow-hidden transition-all duration-300 hover:shadow-xl"
                >
                    <div class="px-6 py-5 bg-gray-50 dark:bg-gray-900/50 border-b dark:border-gray-700 flex items-center space-x-4">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-600 dark:text-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="section.icon" />
                        </svg>
                        <div>
                            <h3 class="text-lg font-semibold text-gray-900 dark:text-gray-100">
                                {{ section.title }}
                            </h3>
                            <p class="text-sm text-gray-500 dark:text-gray-400">
                                {{ section.description }}
                            </p>
                        </div>
                    </div>
                    <div class="p-6">
                        <component 
                            :is="section.component"
                            :must-verify-email="mustVerifyEmail"
                            :status="status"
                            class="max-w-full"
                        />
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>

<style scoped>
/* Subtle animation for hover effect */
.hover\:shadow-xl {
    transition: all 0.3s ease;
}
.hover\:shadow-xl:hover {
    transform: translateY(-5px);
}
</style>