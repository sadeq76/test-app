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

const counter = ref<number>(0);

const increase = (): number => counter.value++;
const descrease = (): number | void =>
  counter.value > 0 ? counter.value-- : undefined;

const request: IDBOpenDBRequest = indexedDB.open('counterDataBase', 3);

let db: IDBDatabase | undefined = undefined;
let store: IDBObjectStore | undefined = undefined;

request.onupgradeneeded = (e) => {
  // eslint-disable-next-line @typescript-eslint/ban-ts-comment
  //@ts-ignore
  db = e.target.result;
  if (!db?.objectStoreNames.contains('counters')) {
    store = db?.createObjectStore('counters', { keyPath: 'name' });
  }
  store?.add({ name: 'counter', value: counter.value });
};
</script>