<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import Elearning from '@/Pages/Elearning.vue';
</script>

<template>
    <Head title="Dashboard" />
    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-2xl font-bold text-black dark:text-black">
                Dashboard
            </h2>
        </template>
        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <!-- E-learning Page -->
                <div class="mt-6">
                    <Elearning />
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
