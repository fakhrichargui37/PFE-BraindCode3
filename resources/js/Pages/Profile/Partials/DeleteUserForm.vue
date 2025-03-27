<script setup>
import DangerButton from '@/Components/DangerButton.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import Modal from '@/Components/Modal.vue';
import SecondaryButton from '@/Components/SecondaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { useForm } from '@inertiajs/vue3';
import { nextTick, ref, computed } from 'vue';

const confirmingUserDeletion = ref(false);
const passwordInput = ref(null);
const confirmationText = ref('');

const form = useForm({
    password: '',
});

const confirmationPhrase = 'DELETE MY ACCOUNT';

const isConfirmationValid = computed(() => 
    confirmationText.value.toUpperCase() === confirmationPhrase
);

const confirmUserDeletion = () => {
    confirmingUserDeletion.value = true;
    confirmationText.value = '';

    nextTick(() => passwordInput.value.focus());
};

const deleteUser = () => {
    if (!isConfirmationValid.value) return;

    form.delete(route('profile.destroy'), {
        preserveScroll: true,
        onSuccess: () => closeModal(),
        onError: () => passwordInput.value.focus(),
        onFinish: () => {
            form.reset();
            confirmationText.value = '';
        },
    });
};

const closeModal = () => {
    confirmingUserDeletion.value = false;
    form.clearErrors();
    form.reset();
    confirmationText.value = '';
};
</script>

<template>
    <div class="max-w-xl mx-auto bg-white dark:bg-gray-800 shadow-lg rounded-lg p-6 space-y-6">
        <header class="border-b pb-4 mb-6 dark:border-gray-700">
            <h2 class="text-2xl font-bold text-red-600 dark:text-red-400">
                Delete Account
            </h2>

            <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
                Permanently delete your account and all associated data. This action cannot be undone.
            </p>
        </header>

        <div class="bg-red-50 dark:bg-red-900/30 border border-red-200 dark:border-red-700 p-4 rounded-lg space-y-4">
            <div class="flex items-center space-x-3">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-red-500 dark:text-red-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
                </svg>
                <h3 class="text-lg font-semibold text-red-700 dark:text-red-300">
                    Irreversible Action
                </h3>
            </div>

            <p class="text-sm text-gray-700 dark:text-gray-300">
                Deleting your account will:
                <ul class="list-disc list-inside space-y-1 mt-2 text-white">
                <li>Permanently remove all your personal data</li>
                <li>Delete all associated account resources</li>
                <li>Revoke access to all services</li>
                <li>Cannot be recovered or restored</li>
            </ul>
            </p>
        </div>

        <DangerButton 
            @click="confirmUserDeletion"
            class="w-full justify-center py-2 hover:bg-red-700 transition-colors"
        >
            Delete Account
        </DangerButton>

        <Modal :show="confirmingUserDeletion" @close="closeModal">
            <div class="p-6 bg-white dark:bg-gray-800">
                <h2 class="text-lg font-medium text-gray-900 dark:text-gray-100 mb-4">
                    Confirm Account Deletion
                </h2>

                <div class="space-y-4">
                    <p class="text-sm text-gray-600 dark:text-gray-400">
                        This is a permanent and irreversible action. To confirm, 
                        please enter your password and type <strong>{{ confirmationPhrase }}</strong> 
                        in the text field below.
                    </p>

                    <div>
                        <InputLabel 
                            for="password" 
                            value="Confirm Password" 
                            class="mb-2 font-semibold text-gray-700 dark:text-gray-300"
                        />
                        <TextInput
                            id="password"
                            ref="passwordInput"
                            v-model="form.password"
                            type="password"
                            class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                                   focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all duration-300"
                            placeholder="Enter your password"
                            @keyup.enter="deleteUser"
                            required
                        />
                        <InputError :message="form.errors.password" class="mt-2 text-red-500" />
                    </div>

                    <div>
                        <InputLabel 
                            for="confirmation" 
                            value="Confirmation" 
                            class="mb-2 font-semibold text-gray-700 dark:text-gray-300"
                        />
                        <TextInput
                            id="confirmation"
                            v-model="confirmationText"
                            type="text"
                            class="mt-1 block w-full rounded-md shadow-sm dark:bg-gray-700 dark:text-gray-300 dark:border-gray-600 
                                   focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all duration-300"
                            placeholder="Type 'DELETE MY ACCOUNT' to confirm"
                            required
                        />
                    </div>
                </div>

                <div class="mt-6 flex justify-end space-x-3">
                    <SecondaryButton 
                        @click="closeModal"
                        class="px-4 py-2"
                    >
                        Cancel
                    </SecondaryButton>

                    <DangerButton
                        @click="deleteUser"
                        :disabled="!isConfirmationValid || form.processing"
                        class="px-4 py-2 flex items-center space-x-2"
                    >
                        <span>Delete Account</span>
                    </DangerButton>
                </div>
            </div>
        </Modal>
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