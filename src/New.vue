<script setup>
import { ref } from 'vue'
import { createApp } from "vue";
import App from "./App.vue";
const list = ref([])

function addNewList() {
  if (list._rawValue.length > 9) {
    document.querySelector("#button").innerText = "❌ You can't add more!"
    setTimeout(() => {
      document.querySelector("#button").innerText = "+ Add another card"
    }, 3000);
  } else {
    // Add input to todos
    list.value.push("Untitled")

    const lists = document.createElement("div")
    const newCard = document.querySelector("#new")
    lists.id = "app"

    newCard.before(lists)

    createApp(App).mount(lists);
    localStorage.setItem('lists', JSON.stringify(list));
    console.log(list)
  }
}

</script>

<template>
  <main>
    <button @click="addNewList" id="button">+ Add another card</button>
  </main>
</template>

<style scoped>
main {
  margin-left: 25px;
  margin-top: 5px;
  color: black;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  width: 215px;
}

button {
  padding: 10px;
  border-radius: 5px;
  background: #dddddd;
  color: black;
  margin-right: 25px;
}

button:hover {
  background: white;
}
</style>