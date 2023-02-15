<script setup>
import { ref } from 'vue'
import { createApp } from "vue";
import App from "./App.vue";
const newList = ref('')
const list = ref(["Untitled"])

function addNewList() {
  // Check if input is empty
  if (!newList.value) return
  // Add input to todos
  list.value.push(newList.value)
  // Clear input
  newList.value = ''

  const lists = document.createElement("div")
  const newCard = document.querySelector("#new")
  lists.id = "app"

  newCard.before(lists)

  createApp(App).mount(lists);
  for (let i = 0; i < list.length; i++) {
    document.querySelector("#cardTitle").innerText = list[i].value
  }
  console.log(list)
}

</script>

<template>
  <main>
    <input type="text" placeholder="+ Add another list" v-model="newList" @keypress.enter="addNewList">
  </main>
</template>

<style scoped>
main {
  margin-left: 30px;
  margin-top: 5px;
  color: black;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  max-width: 250px;
}

input {
  width: auto;
  padding: 10px;
  border-radius: 5px;
  background: #dddddd;
  color: black;
  outline: none;
  margin-right: 25px;
}

input:hover {
  background: white;
}

input:focus {
  background: white;
}
</style>