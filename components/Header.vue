<script lang="ts" setup>
import { useUsersStore } from '~/store/users';
import { useAuthStore } from '~/store/auth';

const usersStore = useUsersStore();
const authStore = useAuthStore();
const { getUserWithID } = usersStore;
const { signOut } = authStore;

const user: Ref<any> = ref();
const statusDropdown = ref('');
const loading = ref(true);
let loginId = '';

async function logout() {
    if(process.client) {
        localStorage.removeItem('login');
        loginId = localStorage.getItem('login') || '';
        await signOut();
        await navigateTo('/');
        reloadNuxtApp();
    }
}
const route = useRoute();

watch(() => route.fullPath, () => {
    loginId = localStorage.getItem('login') || "";
});
function toggleDropdown() {
    statusDropdown.value = statusDropdown.value ? "" : "active";
}

onMounted(async () => {
    loading.value = true;
    if(process.client) {
        loginId = localStorage.getItem('login') || "";
        user.value = await getUserWithID(loginId);
        loading.value = false;
    }
});
</script>
<template>
    <div class="header">
        <div v-if="loading" class="loading"></div>
        <div v-if="loading" class="loading-bg"></div>
        <h1 class="title">Mark_<span class="line-troughed">/\</span>t</h1>
        <ul class="menu-bar">
            <NuxtLink to="/" class="link">HOME</NuxtLink>
            <NuxtLink to="/products" class="link">PRODUCTS</NuxtLink>
            <NuxtLink to="/cart" class="link">CART</NuxtLink>
            <NuxtLink v-if="!user" to="/login" class="link">LOGIN</NuxtLink>
            <div class="profile link" v-else>
                <img @click="toggleDropdown" :src="'https://zvsrlgorpzpomrugkfjd.supabase.co/storage/v1/object/public/avatars/'+user.avatar" width="50" height="50">
                <ul :class="statusDropdown">
                    <li>{{ user.name }}</li>
                    <li @click="logout">logout</li>
                </ul>
            </div>
        </ul>
    </div>
</template>