<script setup>
import PriorityDot from './PriorityDot.vue';
const props = defineProps(['data'])
const emit = defineEmits(['edit', 'delete', 'check'])
</script>
<template>
    <div class="flex justify-between w-full bg-white rounded-lg shadow p-4 my-4">
        <div class="flex gap-3">
            <span @click="emit('check')" class="w-8 h-8 border-2 rounded-md " data-cy="todo-item-checkbox"
                :class="!data.is_active ? 'border-emerald-500 text-emerald-500' : 'border-gray-300 text-transparent'">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                    stroke="currentColor" class="w-6 h-6 mx-auto">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" />
                </svg>
            </span>
            <span class="px-1 my-auto">
                <!-- priority -->
                <PriorityDot :priority="data.priority" />
            </span>
            <span class="text-lg my-auto font-semibold" :class="!data.is_active ? 'text-gray-500 line-through' : ''"
                data-cy="todo-item-title">
                {{ data.title }}
            </span>
            <span @click="emit('edit')" class="text-gray-500" data-cy="todo-item-edit-button">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                    stroke="currentColor" class="w-4 h-4 mt-2">
                    <path stroke-linecap="round" stroke-linejoin="round"
                        d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10" />
                </svg>
            </span>
        </div>
        <div @click="emit('delete')" class="px-2 py-1 my-auto" data-cy="todo-item-delete-button">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                <path fill-rule="evenodd"
                    d="M8.75 1A2.75 2.75 0 006 3.75v.443c-.795.077-1.584.176-2.365.298a.75.75 0 10.23 1.482l.149-.022.841 10.518A2.75 2.75 0 007.596 19h4.807a2.75 2.75 0 002.742-2.53l.841-10.52.149.023a.75.75 0 00.23-1.482A41.03 41.03 0 0014 4.193V3.75A2.75 2.75 0 0011.25 1h-2.5zM10 4c.84 0 1.673.025 2.5.075V3.75c0-.69-.56-1.25-1.25-1.25h-2.5c-.69 0-1.25.56-1.25 1.25v.325C8.327 4.025 9.16 4 10 4zM8.58 7.72a.75.75 0 00-1.5.06l.3 7.5a.75.75 0 101.5-.06l-.3-7.5zm4.34.06a.75.75 0 10-1.5-.06l-.3 7.5a.75.75 0 101.5.06l.3-7.5z"
                    clip-rule="evenodd" />
            </svg>
        </div>
    </div>
</template>