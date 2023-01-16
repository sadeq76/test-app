<template>
  <q-card class="no-border-radius q-pa-lg column items-center">
    <h1 class="text-h1">step counter</h1>
    <p class="text-h3">
      steps:
      <span>{{ counter }}</span>
    </p>
    <div class="flex justify-between full-width q-mt-lg">
      <q-btn @click="descrease" color="blue-grey-6" label="-" />
      <q-btn @click="increase" color="blue-grey-6" label="+" />
    </div>
  </q-card>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const counter = ref<number>(Number(localStorage.getItem('counter')));

const increase = (): number => counter.value++;
const descrease = (): number | void =>
  counter.value > 0 ? counter.value-- : undefined;

const updateStorage = () => {
  localStorage.setItem('counter', counter.value.toString());
};

window.addEventListener('beforeunload', updateStorage);
</script>
