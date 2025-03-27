<script setup>
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { useForm } from '@inertiajs/vue3';
import { ref, computed } from 'vue';

const passwordInput = ref(null);
const currentPasswordInput = ref(null);

const form = useForm({
    current_password: '',
    password: '',
    password_confirmation: '',
});

const passwordStrength = computed(() => {
    const password = form.password;
    if (password.length < 8) return 'weak';
    let strength = 0;
    if (password.match(/[a-z]+/)) strength++;
    if (password.match(/[A-Z]+/)) strength++;
    if (password.match(/[0-9]+/)) strength++;
    if (password.match(/[$@#&!]+/)) strength++;
    return strength < 3 ? 'moderate' : 'strong';
});

const updatePassword = () => {
    form.put(route('password.update'), {
        preserveScroll: true,
        onSuccess: () => {
            form.reset();
            // Optional: Add a toast or notification
        },
        onError: () => {
            if (form.errors.password) {
                form.reset('password', 'password_confirmation');
                passwordInput.value.focus();
            }
            if (form.errors.current_password) {
                form.reset('current_password');
                currentPasswordInput.value.focus();
            }
        },
    });
};
</script>

<template>
    <div class="max-w-xl mx-auto bg-white dark:bg-gray-800 shadow-lg rounded-lg p-6 space-y-6">
        <header class="border-b pb-4 mb-6 dark:border-gray-700">
            <h2 class="text-2xl font-bold text-gray-900 dark:text-gray-100">
                Update Password
            </h2>

            <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
                Protect your account with a strong, unique password. 
                Use a combination of uppercase, lowercase, numbers, and symbols.
            </p>
        </header>

        <form @submit.prevent="updatePassword" class="space-y-6">
            <div>
                <InputLabel 
                    for="current_password" 
                    value="Current Password" 
                    class="mb-2 font-semibold text-gray-700 dark:text-gray-300"
                />

                <TextInput
                    id="current_password"
                    ref="currentPasswordInput"
                    v-model="form.current_password"
                    type="password"
                    class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                           focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all duration-300"
                    autocomplete="current-password"
                    required
                />

                <InputError
                    :message="form.errors.current_password"
                    class="mt-2 text-red-500"
                />
            </div>

            <div>
                <InputLabel 
                    for="password" 
                    value="New Password" 
                    class="mb-2 font-semibold text-gray-700 dark:text-gray-300"
                />

                <TextInput
                    id="password"
                    ref="passwordInput"
                    v-model="form.password"
                    type="password"
                    class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                           focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all duration-300"
                    autocomplete="new-password"
                    required
                />

                <div class="mt-2 flex items-center">
                    <div 
                        class="w-full h-2 mr-2 rounded transition-all"
                        :class="{
                            'bg-red-500': passwordStrength === 'weak',
                            'bg-yellow-500': passwordStrength === 'moderate',
                            'bg-green-500': passwordStrength === 'strong'
                        }"
                    ></div>
                    <span 
                        class="text-sm font-medium"
                        :class="{
                            'text-red-500': passwordStrength === 'weak',
                            'text-yellow-500': passwordStrength === 'moderate',
                            'text-green-500': passwordStrength === 'strong'
                        }"
                    >
                        {{ passwordStrength === 'weak' ? 'Weak' : 
                           passwordStrength === 'moderate' ? 'Moderate' : 'Strong' }}
                    </span>
                </div>

                <InputError 
                    :message="form.errors.password" 
                    class="mt-2 text-red-500" 
                />
            </div>

            <div>
                <InputLabel
                    for="password_confirmation"
                    value="Confirm New Password"
                    class="mb-2 font-semibold text-gray-700 dark:text-gray-300"
                />

                <TextInput
                    id="password_confirmation"
                    v-model="form.password_confirmation"
                    type="password"
                    class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                           focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all duration-300"
                    autocomplete="new-password"
                    required
                />

                <InputError
                    :message="form.errors.password_confirmation"
                    class="mt-2 text-red-500"
                />
            </div>

            <div class="flex items-center justify-between">
                <PrimaryButton 
                    :disabled="form.processing"
                    class="px-6 py-2 rounded-md transition-all duration-300 
                           hover:shadow-md active:scale-95"
                >
                    {{ form.processing ? 'Updating...' : 'Update Password' }}
                </PrimaryButton>

                <Transition name="fade">
                    <p
                        v-if="form.recentlySuccessful"
                        class="text-sm text-green-600 dark:text-green-400"
                    >
                        Password updated successfully!
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