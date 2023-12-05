<script setup>
import { computed } from 'vue';
const props = defineProps({
    activity: Object
})
const emit = defineEmits(['delete', 'detail'])
const date = computed(() => {
    const dates = new Date(props.activity.created_at)
    return dates.toLocaleDateString('id-ID', { year: 'numeric', month: 'long', day: 'numeric' })
})

const handleDelete = () => {
    emit('delete', props.activity)
}
</script>
<template>
    <div class="p-4 border rounded-lg shadow bg-white h-60 flex flex-col justify-between">
        <div class="h-full" @click="emit('detail')" data-cy="activity-item">
            <h4 class="text-xl text-slate-800 font-bold" data-cy="activity-item-title">{{ activity.title }}</h4>
        </div>
        <div class="flex justify-between text-slate-600">
            <div data-cy="activity-item-date">
                {{ date }}
            </div>
            <div @click="handleDelete" class="cursor-pointer p-2" data-cy="activity-item-delete-button">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                    <path fill-rule="evenodd"
                        d="M8.75 1A2.75 2.75 0 006 3.75v.443c-.795.077-1.584.176-2.365.298a.75.75 0 10.23 1.482l.149-.022.841 10.518A2.75 2.75 0 007.596 19h4.807a2.75 2.75 0 002.742-2.53l.841-10.52.149.023a.75.75 0 00.23-1.482A41.03 41.03 0 0014 4.193V3.75A2.75 2.75 0 0011.25 1h-2.5zM10 4c.84 0 1.673.025 2.5.075V3.75c0-.69-.56-1.25-1.25-1.25h-2.5c-.69 0-1.25.56-1.25 1.25v.325C8.327 4.025 9.16 4 10 4zM8.58 7.72a.75.75 0 00-1.5.06l.3 7.5a.75.75 0 101.5-.06l-.3-7.5zm4.34.06a.75.75 0 10-1.5-.06l-.3 7.5a.75.75 0 101.5.06l.3-7.5z"
                        clip-rule="evenodd" />
                </svg>
            </div>
        </div>
    </div>
</template>