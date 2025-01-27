<script setup lang="ts">
import { useForm, useField } from 'vee-validate';
import { reactive } from 'vue';

interface User {
  name: string;
  email: string;
  createAt?: string;
  _id?: string;
}

const state = reactive<{ users: User[] }>({
  users: [],
});

const { handleSubmit, resetForm } = useForm();

const mySubmit = handleSubmit(async (value) => {
  try{
    const response = await fetch('https://restapi.fr/api/ListUsers',{
      method: 'POST',
      body: JSON.stringify(value),
      headers: {
        'Content-Type': 'application/json',
      },
    });
    const user: User = await response.json();
    state.users.push(user);
    resetForm();
  }catch(e){
    console.error(e);
  }
});

const { value: emailValue } = useField('email');
const { value: nameValue } = useField('name');
</script>

<template>
  <div class="container">
    <div class="p-20">
      <h3 class="mb-10">Form</h3>
      <form @submit="mySubmit">
        <input 
              v-model="nameValue"
              class="mr-10"
              type="text"
              placeholder="First Name"
        />
        <input 
              v-model="emailValue"
              class="mr-10"
              type="text"
              placeholder="Email"
        />
        <button class="btn btn-primary">Save</button>
      </form>
    </div>
    <div class="p-20">
      <h3>List of Users</h3>
      <ul>
        <li v-for="user in state.users">
            <p>{{ user.name }} - {{ user.email }}</p>
        </li>
      </ul>
    </div>

  </div>
</template>

<style lang="scss">
@use './assets/scss/base.scss';
</style>