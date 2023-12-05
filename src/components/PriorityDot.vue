<script setup>
import { ref, computed } from 'vue'
const props = defineProps(['priority']);
const priorities = [
    'very-high',
    'high',
    'normal',
    'low',
    'very-low'
]
const colors = [
    'bg-red-400',
    'bg-orange-400',
    'bg-green-400',
    'bg-blue-400',
    'bg-indigo-400',
]
const color = computed(() => {
    return colors[priorities.indexOf(props.priority)]
})
</script>
<template>
    <div data-cy="todo-item-priority-indicator" class="w-4 h-4 rounded-full" :class="color"></div>
</template>