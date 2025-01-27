<script setup lang="ts">
import { useForm, useField } from 'vee-validate';
import { reactive } from 'vue';
import { z } from 'zod';
import { toTypedSchema } from '@vee-validate/zod';

const validationSchema = z.object({
  name: z
        .string()
        .min(3,{ message : 'Too short ! '})
        .max(20, { message: 'Too long ! '}),
  email: z
         .string()
         .email({ message: 'Invalid email format ! '}), 
});
interface User {
  name: string;
  email: string;
  createAt?: string;
  _id?: string;
}

const state = reactive<{ users: User[] }>({
  users: [],
});

const { handleSubmit, resetForm } = useForm({
  validationSchema: toTypedSchema(validationSchema)
});

const mySubmit = handleSubmit(async (value) => {
  console.log('Submitted values:', value); // Débogage
  if (!value || typeof value !== 'object') {
    console.error('Values are invalid:', value);
    return;
  }
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

async function fetchUsers(){
  try{
    const response = await fetch('https://restapi.fr/api/ListUsers');
    const users: User | User[] = await response.json();
    if(users){
      state.users = Array.isArray(users) ? users : [users];
    }
  }catch(e){
    console.error(e);
  }
}

async function deleteUser(userId?: string){
  try{
    if(userId){
      await fetch(`https://restapi.fr/api/ListUsers?id=${userId}`, {
          method: 'DELETE',
      });

      state.users = state.users.filter((user) => user._id !== userId)
    }
  }catch(e){
    console.error(e);
  }
}

fetchUsers();

const { value: emailValue, errorMessage: emailError } = useField('email', { validateOnUpdate: false});
const { value: nameValue, errorMessage: nameError } = useField('name');
</script>

<template>
  <div class="container">
    <div class="p-20">
      <h3 class="mb-10">Form</h3>
      <form @submit.prevent="mySubmit">
        <input 
              v-model="nameValue"
              class="mr-10"
              type="text"
              placeholder="First Name"
        />
         <!-- Affichage des erreurs pour Name -->
         <span v-if="nameError">{{ nameError }}</span>
        <input 
              v-model="emailValue"
              class="mr-10"
              type="text"
              placeholder="Email"
        />
        <!-- Affichage des erreurs pour Email -->
        <span v-if="emailError">{{ emailError }}</span>
        <button class="btn btn-primary">Save</button>
      </form>
    </div>
    <div class="p-20">
      <h3>List of Users</h3>
      <ul>
        <li v-for="user in state.users">
            <p>{{ user.name }} - {{ user.email }}</p>
            <button
            @click="deleteUser(user._id)"
            type="button"
            class="btn btn-danger"
          >
            Supprimer
          </button>
        </li>
      </ul>
    </div>

  </div>
</template>

<style lang="scss">
@use './assets/scss/base.scss';
</style>