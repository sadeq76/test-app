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
import { log } from 'console';
import { onBeforeUnmount, onMounted, ref } from 'vue';

const counter = ref<number>(0);

const increase = (): number => counter.value++;
const descrease = (): number | void =>
  counter.value > 0 ? counter.value-- : undefined;

const request: IDBOpenDBRequest = indexedDB.open('counterDataBase', 8);

let db: IDBDatabase | undefined = undefined;
let store: IDBObjectStore | undefined = undefined;

request.onerror = (event) => {
  // Handle errors.
};

const customerData = [
  { ssn: '444-44-4444', name: 'Bill', age: 35, email: 'bill@company.com' },
  { ssn: '555-55-5555', name: 'Donna', age: 32, email: 'donna@home.org' },
];

request.onupgradeneeded = (event) => {
  const db = event.target.result;

  // Create an objectStore to hold information about our customers. We're
  // going to use "ssn" as our key path because it's guaranteed to be
  // unique - or at least that's what I was told during the kickoff meeting.
  const objectStore = db.createObjectStore('customers', { keyPath: 'ssn' });

  // Create an index to search customers by name. We may have duplicates
  // so we can't use a unique index.
  objectStore.createIndex('search by name', 'name', { unique: false });

  // Create an index to search customers by email. We want to ensure that
  // no two customers have the same email, so use a unique index.
  objectStore.createIndex('search by email', 'email', { unique: true });

  // Use transaction oncomplete to make sure the objectStore creation is
  // finished before adding data into it.
  objectStore.transaction.oncomplete = (event) => {
    console.log(event);

    // Store values in the newly created objectStore.
    const customerObjectStore = db
      .transaction('customers', 'readwrite')
      .objectStore('customers');
    customerData.forEach((customer) => {
      console.log(customer);
      customerObjectStore.add(customer);
    });
  };
};

onMounted(() => {
  console.log(db);
});
onBeforeUnmount(() => {
  console.log(db);
});
</script>
