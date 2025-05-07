<template>
    <div class="min-h-screen bg-gray-100 text-gray-800">
        <header class="bg-white shadow-sm p-4 flex items-center justify-between">
            <div class="flex items-center gap-3">
                <img src="/src/assets/Logo.png" class="h-10" />
            </div>
            <div class="flex items-center gap-4 text-gray-500">
                <i class="i-lucide-bell w-5 h-5" />
                <i class="i-lucide-settings w-5 h-5" />
                <i class="i-lucide-log-out w-5 h-5" />
            </div>
        </header>

        <div class="bg-cyan-500 p-6 text-white flex items-center justify-between">
            <div class="flex items-center gap-4">
                <img src="/src/assets/Avatar.png"
                    class="w-16 h-16" />
                <div>
                    <div class="text-2xl font-bold">John Doe</div>
                    <div class="text-sm opacity-80">Last online: 2 days ago</div>
                </div>
            </div>
            <div class="flex gap-3">
                <button class="bg-white text-cyan-600 px-4 py-2 rounded flex items-center gap-2">
                    <i class="i-lucide-send w-4 h-4" /> Send Message
                </button>
                <button class="border border-white px-4 py-2 rounded flex items-center gap-2">
                    <i class="i-lucide-user-plus w-4 h-4" /> Add Friend
                </button>
            </div>
        </div>

        <div class="p-4">
            <div class="flex justify-between mb-4">
                <input v-model="searchQuery" type="text" placeholder="Search by name or email"
                    class="w-full max-w-sm px-4 py-2 rounded border border-gray-300 focus:ring-2 focus:ring-cyan-500" />
                <select v-model="sortOption" class="ml-4 px-3 py-2 border rounded border-gray-300 text-sm">
                    <option value="default">Sort: Default</option>
                    <option value="az">Sort: Name (A–Z)</option>
                    <option value="za">Sort: Name (Z–A)</option>
                </select>
            </div>

            <div class="grid grid-cols-5 text-sm text-gray-500 font-semibold px-2 mb-2">
                <div>Date</div>
                <div>Name</div>
                <div>Gender</div>
                <div>Country</div>
                <div>Email</div>
            </div>

            <div v-if="visibleUsers.length > 0" class="space-y-2">
                <UserCard v-for="user in visibleUsers" :key="user.login.uuid" :user="user" @click="openModal(user)" />
            </div>
            <div v-else class="text-center text-gray-400 py-12">No users found</div>

            <div class="flex justify-center mt-6">
                <button class="bg-cyan-500 text-white px-6 py-2 rounded hover:bg-cyan-600 flex items-center gap-2"
                    @click="fetchUsers">
                    <i class="i-lucide-refresh-ccw w-4 h-4" /> Refresh
                </button>
            </div>
        </div>

        <UserModal v-if="selectedUser" :user="selectedUser" @close="selectedUser = null" />
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import UserCard from '@/components/UserCard.vue'
import UserModal from '@/components/UserModal.vue'

const users = ref([])
const selectedUser = ref(null)
const searchQuery = ref('')
const sortOption = ref('default')

const fetchUsers = async () => {
    const res = await fetch('https://randomuser.me/api/?results=20')
    const data = await res.json()
    users.value = data.results
}

const filteredUsers = computed(() => {
    if (!searchQuery.value.trim()) return users.value
    const q = searchQuery.value.toLowerCase()
    return users.value.filter(user => {
        const name = `${user.name.first} ${user.name.last}`.toLowerCase()
        const email = user.email.toLowerCase()
        return name.includes(q) || email.includes(q)
    })
})

const sortedUsers = computed(() => {
    if (sortOption.value === 'default') return filteredUsers.value

    const sorted = [...filteredUsers.value]
    sorted.sort((a, b) => {
        const nameA = `${a.name.first} ${a.name.last}`.toLowerCase()
        const nameB = `${b.name.first} ${b.name.last}`.toLowerCase()
        return sortOption.value === 'az'
            ? nameA.localeCompare(nameB)
            : nameB.localeCompare(nameA)
    })
    return sorted
})

const visibleUsers = computed(() => sortedUsers.value.slice(0, 10))

const openModal = (user) => {
    selectedUser.value = user
}

onMounted(fetchUsers)
</script>