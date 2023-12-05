<script setup>
import PriorityDot from './PriorityDot.vue';
import { ref, onMounted } from 'vue'
const props = defineProps(['editItem']);
const emit = defineEmits(['cancel', 'save']);
const name = ref('')
const prioritySelected = ref('very-high')
const showPriorityDrop = ref(false)
const priorities = [
    'very-high',
    'high',
    'normal',
    'low',
    'very-low'
]
const save = () => {
    if (name.value == '') {
        return
    }
    if (props.editItem.id) {
        const id = props.editItem.id
        emit('save', { id: id, name: name.value, priority: prioritySelected.value })
    } else {
        emit('save', { name: name.value, priority: prioritySelected.value })
    }
}
onMounted(() => {
    if (props.editItem != '') {
        name.value = props.editItem.title
        prioritySelected.value = props.editItem.priority
    }
})
</script>
<template>
    <div class="fixed top-0 left-0 w-full h-screen bg-black bg-opacity-60">
        <div class="max-w-lg mx-auto mt-[calc(40vh_-_128px)]" data-cy="modal-add" v-click-outside="() => emit('cancel')">
            <div class=" bg-white rounded-lg p-8">
                <div class="flex justify-between">
                    <h3 class="text-xl font-bold">{{ editItem != '' ? 'Edit' : 'Tambah' }} list item</h3>
                    <div class="" @click="emit('cancel')" data-cy="modal-delete-close">close</div>
                </div>
                <div class="mt-6">
                    <label for="name" class="uppercase text-gray-500 text-xs font-bold tracking-widest">Todo Name</label>
                    <input v-model="name" type="text" id="name" data-cy="modal-add-name-input"
                        class="block w-full mt-1 py-1 text-lg bg-transparent border-b-2 border-gray-600 focus:outline-none">
                </div>
                <div class="relative mt-4">
                    <label for="priority" class="uppercase text-gray-500 text-xs font-bold tracking-widest">Priority</label>
                    <div @click="showPriorityDrop = !showPriorityDrop" name="priority" data-cy="modal-add-priority-dropdown"
                        class="flex gap-2 w-fit mt-1 py-1 text-lg bg-transparent border-b-2 border-gray-800 focus:outline-none">
                        <div class="flex gap-3 ">
                            <span class="my-auto">
                                <PriorityDot :priority="prioritySelected" />
                            </span>
                            <span>{{ prioritySelected }}</span>
                        </div>
                        <span class="px-2 my-auto">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                                <path fill-rule="evenodd"
                                    d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
                                    clip-rule="evenodd" />
                            </svg>
                        </span>
                    </div>
                    <div v-show="showPriorityDrop"
                        class="absolute w-fit p-2 bg-white rounded border shadow-md overflow-hidden text-lg">
                        <div v-for="priority, index in priorities" :key="index" data-cy="modal-add-priority-item"
                            @click="prioritySelected = priority, showPriorityDrop = false"
                            class="flex gap-3 py-2 px-4 border-b">
                            <span class="my-auto">
                                <PriorityDot :priority="priority" />
                            </span>
                            <span>{{ priority }}</span>
                        </div>
                    </div>
                </div>
                <div class="flex gap-2 justify-end mt-6 font-semibold">
                    <button @click="save" class="bg-blue-600 text-white px-5 py-2 rounded-full"
                        data-cy="modal-add-save-button" :disabled="name == ''">
                        Simpan</button>
                    <button @click="emit('cancel')" class=" text-gray-500 border px-7 py-2 rounded-full"
                        data-cy="modal-add-cancel-button">Batal</button>
                </div>
            </div>
        </div>
    </div>
</template>