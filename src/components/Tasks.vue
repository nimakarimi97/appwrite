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
const body = ref('');
const error = ref('');

async function fetchTasks() {
  tasks.value = await db.listDocuments(DATABASE_ID, COLLECTION_ID_TASKS);
  console.log("🚀 ~ fetchTasks ~ tasks.value:", tasks.value)
}

onMounted(async () => {
  fetchTasks();
});

async function addTasks() {
  if (!body.value) {
    error.value = 'Task cannot be empty';

    setTimeout(() => {
      error.value = '';
    }, 5000);

  }

  await db.createDocument(DATABASE_ID, COLLECTION_ID_TASKS, 'unique()', {
    body: body.value
  });

  body.value = '';
  fetchTasks();
}

async function deleteTask(id) {
  await db.deleteDocument(DATABASE_ID, COLLECTION_ID_TASKS, id);
  fetchTasks();
}

</script>

<template>
  <!-- <h3>{{ msg }}</h3> -->

  <span v-if="error" class="error">{{ error }}</span>

  <div class="new-task-container">
    <input type="text" class="new-task" v-model="body" placeholder="Add task" @keyup.enter="addTasks">
    <button class="new-task" @click="addTasks" type="submit" :disabled="!body">
      <p class="submit">submit</p>
    </button>
  </div>

  <div class="card">
    <button type="button" @click="count++"> {{ tasks.total || 'loading...' }}</button>
    <button type="button" @click="fetchTasks"> fetchTasks</button>
  </div>


  <div class="task-list" v-for="task in tasks.documents" :key="task.$id">
    <input type="checkbox" name="task" :checked="task.completed">


    <p :class="task.completed ? 'completed' : ''">{{ task.body }}</p>
    <strong class="delete" :id="`delete-${task.$id}`" @click="deleteTask(task.$id)">x</strong>
  </div>
</template>

<style scoped lang="scss">
.new-task-container {
  display: flex;
  gap: 1rem;
  justify-content: center;
  align-items: center;

  input.new-task {
    border: none;
    outline: none;
    border-radius: 15px;
    padding: 1em;
    background-color: #ccc;
    box-shadow: inset 2px 5px 10px rgba(0, 0, 0, 0.3);
    transition: 300ms ease-in-out;

    &:focus {
      background-color: rgb(197, 197, 197);
      color: black;
      transform: scale(1.05);
      box-shadow: 0px 0px 6px #969696, 1px 2px 12px #ffffff;
    }
  }

  button.new-task {
    width: 90px;
    height: 40px;
    position: relative;
    font-family: var(--font);
    color: #3b82f6 !important;
    font-weight: 600;
    background-color: #fff;
    border: none;
    overflow: hidden;
    border-radius: 5px;
    box-shadow: rgba(0, 0, 0, 0.1) 0px 1px 2px 0px;
    transition: all ease 100ms;

    p {
      margin: 0;
    }
  }

  button:hover {
    background-color: #cbdcf8;
  }

  button:focus {
    background-color: #cbdcf8;
  }

  button::before {
    content: 'done✅';
    position: absolute;
    color: #3b82f6;
    left: 0;
    top: -14px;
    right: 0;
    transition: all ease 300ms;
    opacity: 0%;
  }

  button:focus::before {
    opacity: 100%;
    transform: translateY(23px);
  }

  .submit {
    transition: all ease 100ms;
    opacity: 100%;
    color: #3b82f6 !important;
  }

  button:focus>.submit {
    opacity: 0%;
  }
}

.task-list {
  display: flex;
  justify-content: start;
  align-items: center;
  padding: .4rem;
  border-radius: 10px;
  border: 1px solid var(--color-dark);
  margin: 1rem 0;
  gap: 1rem;
  max-width: 300px;

  p {
    margin: 0 .5rem;
    text-align: start;
  }

  .delete {
    color: #FE4E6F;
    font-size: 22px;
    cursor: pointer;
    margin: 0 5% 0 auto;
  }

  .completed {
    text-decoration: line-through;
    text-decoration-thickness: 2px;
    text-decoration-color: var(--color-dark);
    font-style: italic;
  }
}

.error {
  color: white;
  position: absolute;
  top: 1rem;
  left: 3%;
  background-color: #d41010;
  padding: .5rem;
  border-radius: 5px;
  text-align: center;
}
</style>
