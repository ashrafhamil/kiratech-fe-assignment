<template>
    <div class="grid grid-cols-5 items-center bg-white rounded-lg shadow-sm px-4 py-4 hover:ring-2 ring-cyan-500 cursor-pointer"
        @click="$emit('click')">
        <div>{{ formattedDate }}</div>
        <div class="font-semibold text-cyan-800">{{ fullName }}</div>
        <div class="capitalize">{{ user.gender }}</div>
        <div>{{ user.location.country }}</div>
        <div class="truncate">{{ user.email }}</div>
    </div>
</template>

<script setup>
import { computed } from 'vue'
const props = defineProps({ user: Object })

const fullName = computed(() => {
    const { title, first, last } = props.user.name
    return `${first} ${last}`
})

const formattedDate = computed(() => {
    const date = new Date(props.user.registered.date)
    return date.toLocaleDateString('en-GB', {
        day: '2-digit',
        month: 'short',
        year: 'numeric'
    })
})
</script>