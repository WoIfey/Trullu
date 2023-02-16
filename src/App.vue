<script setup>
import { ref } from 'vue'

const newTodo = ref('')
const todos = ref([])
const done = ref([])
const cookie = document.querySelector("#firstTime")

if (document.cookie == "") {
  cookie.style.display = "initial"
}
document.querySelector("#cookies").addEventListener("click", function () {
  cookie.style.display = "none"
  document.cookie = "accepted";
})

function addNewTodo() {
  if (todos._rawValue.length > 9) {
    document.querySelector(".addCard").innerText = "❌ Too many uncompleted cards!"
    setTimeout(() => {
      document.querySelector(".addCard").innerText = "+ Add a card"
    }, 3000);
  }
  else if (done._rawValue.length > 49) {
    document.querySelector(".addCard").innerText = "❌ Too many completed cards!"
    setTimeout(() => {
      document.querySelector(".addCard").innerText = "+ Add a card"
    }, 3000);
  } else {
    // Check if input is empty
    if (!newTodo.value) return

    // Add input to todos
    todos.value.unshift(newTodo.value)

    localStorage.setItem('todos', JSON.stringify(todos));

    // Clear input
    newTodo.value = ''
  }
}

function todoDone(todo, index) {
  // Remove todo at index
  todos.value.splice(index, 1)

  // Add todo to done
  done.value.unshift(todo)
}

function todoUndone(todo, index) {
  // Remove todo at index
  done.value.splice(index, 1)

  // Add todo to done
  todos.value.push(todo)
}

function todoDelete(index) {
  // Remove todo at index
  done.value.splice(index, 1)
}

function visible() {
  document.querySelector(".hidden").style.display = "initial"
  document.querySelector(".addCard").style.display = "none"
  document.querySelector("textarea").focus()
}

function addVisible() {
  document.querySelector(".hidden").style.display = "none"
  document.querySelector(".addCard").style.display = "initial"
}

function edit() {
  const cardTitle = document.querySelector("#cardTitle")
  let edited = prompt("Edit Card Title (Max 25 Characters)");
  edited = edited.substring(0, 25);

  if (edited) {
    cardTitle.innerText = edited;
    localStorage.setItem('cTitle', edited);
  } else {
    cardTitle.innerText = "Untitled";
  }
}
window.onload = function savedCardT() {
  let newObject = window.localStorage.getItem("todos");
  let obj = JSON.parse(newObject)

  const cardTitle = document.querySelector("#cardTitle")
  let cardT = localStorage.getItem("cTitle");
  cardTitle.textContent = cardT
  if (cardT === null) {
    cardTitle.innerText = "Untitled"
  }
}
</script>

<template>
  <main>
    <div id="title">
      <h1 id="cardTitle">Untitled</h1>
      <button id="edit" @click="edit">
        <img src="/src/assets/pencil-simple-line-bold.svg">
      </button>
    </div>

    <span id="cards">
      <ul id="todo">
        <li v-for="(todo, index) in todos" :key="todo">
          <p @click="todoDone(todo, index)">{{ todo }}</p>
        </li>
      </ul>

      <ul id="done">
        <li v-for="(todo, index) in done" :key="todo">
          <p @click="todoUndone(todo, index)">{{ todo }}</p>
          <button @click="todoDelete(index)">❌</button>
        </li>
      </ul>
    </span>

    <div class="card">
      <button @click="visible" class="addCard">+ Add a card</button>
    </div>
    <div class="hiddenAdd">
      <div class="hidden">
        <textarea rows="2" col="0" type="text" placeholder="Enter a title for your card...   (Max 80 Characters)"
          v-model="newTodo" @keypress.enter="addNewTodo(); addVisible();" maxlength="80"></textarea>
        <div class="between">
          <button class="added" @click="addNewTodo(); addVisible();">Add card</button>
          <button @click="addVisible" class="close">❌</button>
        </div>
      </div>
    </div>

</main>
</template>

<style scoped>
.hidden {
  display: none;
}

.hiddenAdd {
  display: flex;
  justify-content: center;
}

.card {
  padding-top: 7px;
  display: flex;
  justify-content: center;
  width: 100%;
}

.addCard {
  width: 100%;
  border-radius: 5px;
}

.addCard:hover {
  background: #d6d6d6;
}

main {
  padding: 10px;
  margin-left: 25px;
  margin-top: 5px;
  background: #e4e4e4;
  color: black;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  width: 250px;
  overflow-x: hidden;
  max-height: 90%;
}

textarea {
  padding: 5px;
  border-radius: 5px;
  background: #dadada;
  color: black;
  width: 230px;
  outline: none;
  resize: none;
  font-size: 15px;
  margin-bottom: 3px;
}

textarea:hover {
  background: white;
}

textarea:focus {
  background: white;
}

h1 {
  padding-bottom: 7px;
  padding-left: 5px;
  font-weight: bold;
  font-size: 20px;
}

#cards {
  overflow-y: auto;
  border-radius: 10px;
}

li {
  border-radius: 5px;
  cursor: pointer;
  padding: 10px;
  background: white;
  box-shadow: 0 1px 1px #b1b1b1;
  margin-bottom: 10px;
  user-select: none;
}

#done li {
  background: #d6d6d6;
}

p {
  white-space: pre-line;
  overflow: hidden;
  text-overflow: ellipsis;
  width: 100%;
  font-size: 15px;
}

#done>li {
  display: flex;
  justify-content: space-between;
}

#done p {
  text-decoration: line-through;
}

#title {
  display: flex;
}

img {
  width: 15px;
}

#edit {
  margin-left: 4px;
  margin-bottom: 5px;
}

.added {
  background: #6e87de;
  box-shadow: 0 1px 1px #b1b1b1;
  color: white;
  padding: 2px 7px;
  border-radius: 10px;
}

.added:hover {
  background: #6074be;
}

.close {
  width: 30px;
  height: 30px;
  background: white;
  box-shadow: 0 1px 1px #b1b1b1;
  border-radius: 10px;
}

.close:hover {
  background: #e6e6e6;
}

.between {
  display: flex;
  justify-content: space-between;
}

#cardTitle {
  max-width: 200px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
</style>