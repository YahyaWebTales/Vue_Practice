<template>
  <form> 
    <div>
      <label>Hobbies</label>
      <div>
          <button @click="push({name: '', good: false })" type="button">
            Add your hobby
          </button>
      </div>
    </div>
    <div v-for="(field, i) in fields" :key="field.key">
      <Field :name="`hobbies[${i}].name`" v-slot="{ field,errorMessage}">
        <input v-bind="field" type="text" />
        <p>{{  errorMessage }}</p>
      </Field>
      <Field :name="`hobbies[${i}].good`" v-slot="{ field }">
        <div>
        <input :id="`good[${i}]`" v-bind="field" type="radio" :value="true" />
          <label :for="`good[${i}]`">Good</label>
        </div>
        <div>
          <input :id="`bad[${i}]`" v-bind="field" type="radio" :value="false" />
          <label :for="`bad[${i}]`">Bad</label>
        </div>
      </Field>
      <button @click="remove(i)"> Remove</button>
      <br />
      <br />
    </div>
    <pre>{{ values }}</pre>
  </form>
</template>

<script setup lang="ts">
import { useForm, useFieldArray, Field } from 'vee-validate';
import { z } from 'zod';
import { toFormValidator } from '@vee-validate/zod';

const validationSchema = toFormValidator(
    z.object({
      hobbies: z
                .array(
                  z.object({
                    name: z 
                          .string({ required_error: 'REQUIRED !!!!'})
                          .min(5, { message: "Too SHORT !!! "}),
                    good: z.string(),
                  })
                )
                .optional(),
    })
);

const { values } = useForm({ validationSchema });
const { fields, push, remove } = useFieldArray('hobbies');
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
