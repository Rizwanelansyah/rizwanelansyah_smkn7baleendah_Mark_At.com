<script lang="ts" setup>
import { useUsersStore } from '~/store/users';
import { useProductsStore } from '~/store/products';

const usersStore = useUsersStore();
const productsStore = useProductsStore();
const { getUserWithID, getCart, setCart } = usersStore;
const { getProductWithId } = productsStore;
const user = ref();
const products: Ref<any[]> = ref([]);
const total = ref(0);
const loading = ref(true);

const formatter = new Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR',
});

definePageMeta({
    middleware: ['auth']
});

async function load() {
    products.value = [];
    total.value = 0;
    let userId: any = '';
    if(process.client) {
        userId = localStorage.getItem('login') || '';
    }
    user.value = await getUserWithID(userId);
    await user.value.incarts.forEach(async (cartInfo: any) => {
        let productBuffer = await getProductWithId(cartInfo[0]) || [{}];
        productBuffer[0].total = cartInfo[1];
        products.value.push(productBuffer[0]);
        total.value += productBuffer[0].price * productBuffer[0].total;
    });
}

onMounted(async () => {
    loading.value = true;
    await load();
    loading.value = false;
});
async function reload(product: any) {
    loading.value = true;
    if(process.client) {
        let userId = localStorage.getItem('login') || '';
        let userCart = await getCart(userId);
        userCart.forEach(async (p: any, index: number) => {
            if(p[0] === product[0]) {
                userCart[index] = product;
            }
        });
        await setCart(userId, userCart);
    }
    await load();
    loading.value = false;
}
async function remove(id: any) {
    loading.value = true;
    if(process.client) {
        let userId = localStorage.getItem('login') || '';
        let userCart = await getCart(userId);
        userCart.forEach(async (p: any, index: number) => {
            if(p[0] === id) {
                userCart.splice(index, 1);
            }
        });
        await setCart(userId, userCart);
    }
    await load();
    loading.value = false;
}
</script>
<template>
    <div class="cart">
        <div v-if="loading" class="loading"></div>
        <div v-if="loading" class="loading-bg"></div>
        <div class="products">
            <template v-for="product in products" :key="product.id">
                <CardCart v-on:remove-from-cart="remove" v-on:amount-change="reload" :prod="product"></CardCart>
            </template>
        </div>
        <div class="total">
            <div class="products">
                <div v-for="product in products" :key="product.id">
                    <p>{{ product.name }}</p>
                    <p>qty : {{ product.total }}</p>
                    <p>total : {{ formatter.format(product.total * product.price) }}</p>
                </div>
            </div>
            <div class="info">
                <div class="total">{{ formatter.format(total) }}</div>
                <button class="btn">Buy</button>
            </div>
        </div>
    </div>
</template>