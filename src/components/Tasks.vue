<script setup>
import { onMounted, ref } from 'vue'
import { Client, Databases } from 'appwrite';

defineProps({
  msg: String,
})


const client = new Client();
const DATABASE_ID = '6617e7266fb6cb92bd5c';
const COLLECTION_ID_TASKS = 'task';


client
  .setEndpoint('https://cloud.appwrite.io/v1')
  .setProject('6617e64315be5c6cf848');

const db = new Databases(client);
const tasks = ref({});

async function fetchTasks() {
  tasks.value = await db.listDocuments(DATABASE_ID, COLLECTION_ID_TASKS);
}

onMounted(async () => {
  fetchTasks();
});


</script>

<template>
  <h3>{{ msg }}</h3>

  <div class="card">
    <button type="button" @click="count++"> {{ tasks.total || 'loading...' }}</button>
    <button type="button" @click="fetchTasks"> fetchTasks</button>

  </div>
</template>

<style scoped></style>
