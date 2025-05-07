<template>
    <div class="fixed inset-0 z-50 bg-black bg-opacity-50 flex items-center justify-center">
        <div class="bg-white w-full max-w-md rounded-xl shadow-xl p-6 relative">
            <button class="absolute top-3 right-4 text-gray-400 hover:text-black" @click="$emit('close')">
                <i class="i-lucide-x w-5 h-5" />
            </button>

            <div class="flex flex-col items-center text-center">
                <img :src="user.picture.large" class="w-24 h-24 rounded-full mb-4" />
                <div class="text-xl font-bold">{{ fullName }}</div>
                <div class="text-sm text-gray-500">{{ user.email }}</div>
                <div class="text-sm mt-2">{{ user.gender }} from {{ user.location.country }}</div>
                <div :class="[
                    'mt-3 text-sm font-medium px-3 py-1 rounded-full',
                    isActive ? 'bg-green-100 text-green-700' : 'bg-red-100 text-red-700'
                ]">
                    {{ isActive ? 'Active' : 'Inactive' }}
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { computed } from 'vue'
const props = defineProps({ user: Object })

const fullName = computed(() => {
    const { first, last } = props.user.name
    return `${first} ${last}`
})

const isActive = computed(() => {
    const seed = props.user.login.uuid
    return seed.charCodeAt(0) % 2 === 0 // simple deterministic toggle
})
</script>