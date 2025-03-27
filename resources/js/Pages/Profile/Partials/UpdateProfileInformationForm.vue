<script setup>
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Link, useForm, usePage } from '@inertiajs/vue3';

defineProps({
    mustVerifyEmail: {
        type: Boolean,
    },
    status: {
        type: String,
    },
});

const user = usePage().props.auth.user;

const form = useForm({
    first_name: user.first_name,
    last_name: user.last_name,
    email: user.email,
});
</script>

<template>
    <div class="max-w-xl mx-auto bg-white dark:bg-gray-800 shadow-lg rounded-lg p-6 space-y-6">
        <header class="border-b pb-4 mb-6 dark:border-gray-700">
            <h2 class="text-2xl font-bold text-gray-900 dark:text-gray-100">
                Profile Information
            </h2>

            <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
                Manage and update your personal details and contact information.
            </p>
        </header>

        <form 
            @submit.prevent="form.patch(route('profile.update'))"
            class="space-y-6"
        >
            <div class="grid grid-cols-2 gap-4">
                <div>
                    <InputLabel 
                        for="first_name" 
                        value="First Name" 
                        class="mb-2 font-semibold text-gray-700 dark:text-gray-300" 
                    />

                    <TextInput
                        id="first_name"
                        type="text"
                        class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                               focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all duration-300"
                        v-model="form.first_name"
                        required
                        autofocus
                        autocomplete="first_name"
                    />

                    <InputError class="mt-2" :message="form.errors.first_name" />
                </div>

                <div>
                    <InputLabel 
                        for="last_name" 
                        value="Last Name" 
                        class="mb-2 font-semibold text-gray-700 dark:text-gray-300" 
                    />

                    <TextInput
                        id="last_name"
                        type="text"
                        class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                               focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all duration-300"
                        v-model="form.last_name"
                        required
                        autocomplete="last_name"
                    />

                    <InputError class="mt-2" :message="form.errors.last_name" />
                </div>
            </div>

            <div>
                <InputLabel 
                    for="email" 
                    value="Email Address" 
                    class="mb-2 font-semibold text-gray-700 dark:text-gray-300" 
                />

                <TextInput
                    id="email"
                    type="email"
                    class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                           focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all duration-300"
                    v-model="form.email"
                    required
                    autocomplete="username"
                />

                <InputError class="mt-2" :message="form.errors.email" />
            </div>

            <div v-if="mustVerifyEmail && user.email_verified_at === null" 
                 class="bg-yellow-50 dark:bg-yellow-900 border border-yellow-200 dark:border-yellow-700 p-4 rounded-lg">
                <p class="text-yellow-800 dark:text-yellow-200 text-sm">
                    Your email address is unverified.
                    <Link
                        :href="route('verification.send')"
                        method="post"
                        as="button"
                        class="ml-2 text-indigo-600 dark:text-indigo-400 hover:underline font-semibold"
                    >
                        Resend Verification Email
                    </Link>
                </p>

                <Transition name="fade">
                    <div
                        v-show="status === 'verification-link-sent'"
                        class="mt-2 text-sm font-medium text-green-600 dark:text-green-400"
                    >
                        A new verification link has been sent to your email address.
                    </div>
                </Transition>
            </div>

            <div class="flex items-center justify-between">
                <PrimaryButton 
                    :disabled="form.processing"
                    class="px-6 py-2 rounded-md transition-all duration-300 
                           hover:shadow-md active:scale-95"
                >
                    {{ form.processing ? 'Saving...' : 'Save Changes' }}
                </PrimaryButton>

                <Transition name="fade">
                    <p
                        v-if="form.recentlySuccessful"
                        class="text-sm text-green-600 dark:text-green-400"
                    >
                        Profile updated successfully!
                    </p>
                </Transition>
            </div>
        </form>
    </div>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
</style>