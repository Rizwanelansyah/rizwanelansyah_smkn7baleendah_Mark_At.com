<script setup lang="ts">
const props = defineProps(['prod']);
const amount = ref(props.prod.total);
const id = ref(props.prod.id);

const formatter = new Intl.NumberFormat('id-ID', {
  style: 'currency',
  currency: 'IDR',
});
</script>
<template>
    <div class="card-cart">
        <div class="img-con">
            <img width="150" :src="'https://zvsrlgorpzpomrugkfjd.supabase.co/storage/v1/object/public/products/'+props.prod.img" alt="">
        </div>
        <div class="info">
            <h2 class="name">
                {{ props.prod.name }}
            </h2>
            <h4>
                in cart : <button @click="amount-- ? amount : amount++">-</button> {{ (amount < 0 || !amount || amount > 9999) ? 1 : amount }} <button @click="amount++">+</button> <input v-model="amount" type="number">
            </h4>
            <h3>
                {{ formatter.format(props.prod.price) }}
            </h3>
            <div class="acts">
                <button @click="$emit('amount-change', [id, amount])" v-if="amount != props.prod.total">SAVE</button>
                <button @click="$emit('remove-from-cart', id)">REMOVE FROM CART</button>
            </div>
        </div>
    </div>
</template>