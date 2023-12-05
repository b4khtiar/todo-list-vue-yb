<script setup>
const props = defineProps(['activity']);
const emit = defineEmits(['cancel', 'confirm']);

</script>
<template>
    <div class="fixed top-0 left-0 w-full h-screen bg-black bg-opacity-60">
        <div class="w-[400px] mx-auto mt-[calc(50vh_-_128px)]" data-cy="modal-delete">
            <div class=" bg-white rounded-lg p-8 text-center">
                <div class="text-red-600" data-cy="modal-delete-icon">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="w-12 h-12 mx-auto">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M12 9v3.75m-9.303 3.376c-.866 1.5.217 3.374 1.948 3.374h14.71c1.73 0 2.813-1.874 1.948-3.374L13.949 3.378c-.866-1.5-3.032-1.5-3.898 0L2.697 16.126zM12 15.75h.007v.008H12v-.008z" />
                    </svg>
                </div>
                <div class="text-lg mt-2" data-cy="modal-delete-title">
                    Apakah anda yakin menghapus activity
                    <span class="font-semibold">
                        “{{ activity.title }}”
                    </span>
                    ?
                </div>
                <div class="flex gap-2 justify-center mt-6 font-semibold">
                    <button @click="emit('confirm')" class="bg-red-600 text-white px-5 py-2 rounded-full"
                        data-cy="modal-delete-confirm-button">ya,
                        hapus</button>
                    <button @click="emit('cancel')" class=" text-gray-500 border px-7 py-2 rounded-full"
                        data-cy="modal-delete-cancel-button">batal</button>
                </div>
            </div>
        </div>
    </div>
</template>