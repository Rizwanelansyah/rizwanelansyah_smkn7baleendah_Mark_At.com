<script lang="ts" setup>
import { useUsersStore } from '~/store/users';
import { useAuthStore } from '~/store/auth';

function validateEmail(email: string) {
  var regex = /\S+@\S+\.\S+/;
  return regex.test(email);
}
function validateUsername(name: string) {
  var regex = /^(?=.{8,20}$)(?![_.])(?!.*[_.]{2})[a-zA-Z0-9._]+(?<![_.])$/;
  return regex.test(name);
}

const user = ref({name: '', email: '', id: ''});
const password = ref('');

const authStore = useAuthStore();
const usersStore = useUsersStore();
const { signUp } = authStore;
const { addUser, checkNewUser } = usersStore;

async function register() {
  if(!validateEmail(user.value.email))return alert('is not an email');
  if(!validateUsername(user.value.name))return alert('the username is not allowed');
  if(password.value.length < 6)return alert('password is to short');
  if(password.value.length > 40)return alert('password is to long');
  let isValid = await checkNewUser(user.value);
  if(!isValid)return alert('incorrect username or password!');
  let userId = await signUp(user.value.email, password.value);
  user.value.id = userId;
  addUser(user.value);
  navigateTo('/login');
}
</script>
<template>
  <form class="login" target="/" @submit.prevent="register">
    <h1>sign up</h1>
    <input type="text" placeholder="username" v-model="user.name">
    <input type="email" placeholder="email address" v-model="user.email">
    <input type="password" placeholder="password" v-model="password">
    <button type="submit">SIGN UP</button>
    <p>Have An Account ? <NuxtLink to="/login">Sign In</NuxtLink></p>
  </form>
</template>