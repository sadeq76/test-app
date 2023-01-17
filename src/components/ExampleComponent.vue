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

<script setup>
import { onMounted, ref } from 'vue';

const counter = ref(0);

const increase = () => counter.value++;
const descrease = () => (counter.value > 0 ? counter.value-- : undefined);

//indexDB

function indexedDBSupport() {
  return 'indexedDB' in window;
}

let request;

const createDatabase = () => {
  if (!indexedDBSupport())
    throw new Error("Your browser doesn't support IndexedBD");

  request = window.indexedDB.open('MyDatabase', 1);
  request.onsuccess = () => {
    console.log('success');
    getData(1);
  };
  request.onupgradeneeded = (e) => {
    const objectStore = request.result.createObjectStore('counter', {
      autoIncrement: true,
    });
    // objectStore.transaction.oncomplete = (event) => {
    //   const customerObjectStore = request.result
    //     .transaction('counter', 'readwrite')
    //     .objectStore('counter');
    //   customerObjectStore.add(12);
    // };
  };
};

// const addData = (data) => {
//   db.transaction('counter', 'readwrite').objectStore('counter').add(data);
// };

const getData = (key) => {
  console.log('data');
  const data = request.result
    .transaction('counter')
    .objectStore('counter')
    .get(key);
  data.onsuccess = (e) => (counter.value = e.target.result);
};

onMounted(() => {
  createDatabase();
  console.log('mounted');
});
</script>
