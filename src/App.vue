<script setup>
import { onMounted, ref, watch } from 'vue'

const lists = ref(["Untitled"])
/* const lists = ref([{
  title: "Untitled",
  items: [{ text: '', done: false }],
}]) */

const newTodo = ref('')
const todos = ref([])
const done = ref([])
const showCookie = ref(false)
const addCardBtn = ref(true)
const addCardTodo = ref(false)

function setCookie() {
  document.cookie = "cookies=accepted";
  showCookie.value = false;
}

// Add new todo
function addNewTodo() {
  // Check if input is empty
  if (!newTodo.value) return

  // Add input to todos
  todos.value.push(newTodo.value)

  localStorage.setItem('todos', JSON.stringify(todos.value));
  localStorage.setItem('done', JSON.stringify(done.value));
  // Clear input
  newTodo.value = ''
}

/* watch(
  () => lists,
  () => {
    localStorage.setItem('todos', JSON.stringify(todos.value));
    localStorage.setItem('done', JSON.stringify(done.value));
  }, { deep: true }
) */

function todoDone(todo, index) {
  // Remove todo at index
  todos.value.splice(index, 1)

  // Add todo to done
  done.value.unshift(todo)
  localStorage.setItem('todos', JSON.stringify(todos.value));
  localStorage.setItem('done', JSON.stringify(done.value));
}

function todoUndo(todo, index) {
  // Remove todo at index
  done.value.splice(index, 1)

  // Add todo to done
  todos.value.push(todo)
  localStorage.setItem('todos', JSON.stringify(todos.value));
  localStorage.setItem('done', JSON.stringify(done.value));
}

function todoDelete(index) {
  // Remove todo at index
  done.value.splice(index, 1)
  localStorage.setItem('done', JSON.stringify(done.value));
}

function show() {
  addCardBtn.value = false
  addCardTodo.value = true
}
function unShow() {
  addCardTodo.value = false
  addCardBtn.value = true
}

// Edits the cards title
function editCard() {
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

// Edits the title
function editTitle() {
  const title = document.querySelector("#Title")
  let renamed = prompt("Rename Title (Max 30 Characters)");
  renamed = renamed.substring(0, 30);
  if (renamed) {
    title.innerText = renamed;
    document.title = renamed + " | Trullu"
    localStorage.setItem("title", renamed);
  } else {
    title.innerText = "Untitled";
  }
}

// Loads all saved cards and title
onMounted(() => {
  // Check if cookie has been accepted
  if (!document.cookie) {
    showCookie.value = true
  }

  // Get todos from localstorage
  let newObject = window.localStorage.getItem("todos");
  let newDones = window.localStorage.getItem("done");
  let obj = JSON.parse(newObject)
  let dones = JSON.parse(newDones)

  if (obj) {
    todos.value = obj
  }
  if (newDones) {
    done.value = dones
  }

  const cardTitle = document.querySelector("#cardTitle")
  let cardT = localStorage.getItem("cTitle");
  cardTitle.textContent = cardT
  if (!cardT) {
    cardTitle.innerText = "Untitled"
  }

  const title = document.querySelector("#Title")
  let titleH = localStorage.getItem("title");
  document.title = titleH + " | Trullu"
  title.textContent = titleH
  if (titleH === null) {
    title.innerText = "Untitled"
    document.title = "Untitled | Trullu"
  }
})

function deleteAllCookies() {
  let text = "Do you want to delete all cards and cookies?";
  if (confirm(text) == true) {
    let Cookies = document.cookie.split(';');
    showCookie.value = true;
    for (let i = 0; i < Cookies.length; i++)
      document.cookie = Cookies[i] + "=;expires=" + new Date(0).toUTCString();
    localStorage.clear();
    location.reload()
  } else {
    return false
  }
}

function addNewList() {
  if (lists.value.length > 6) {
    document.querySelector("#button").innerText = "❌ You can't add more!"
    setTimeout(() => {
      document.querySelector("#button").innerText = "+ Add another card"
    }, 3000);
  } else {
    lists.value.push("Untitled")

  }
}
</script>

<template>
  <nav class="flex justify-between items-center w-screen h-15 fixed bg-zinc-700 border-b-2 border-white pr-2 pl-2">
    <div class="flex items-center w-full">
      <img src="/src/assets/trullu.ico" alt="Icon" class="h-4 ml-2">
      <p class="text-2xl m-2 flex text-white">Trullu</p>
    </div>
    <div class="flex justify-end items-end w-full">
      <img src="/src/assets/trash-bold.svg" alt="Icon"
        class="w-7 p-0.5 mr-1 bg-red-700 rounded-xl cursor-pointer hover:bg-red-800" @click="deleteAllCookies">
    </div>
  </nav>
  <div class="pt-11 fixed flex justify-end items-end">
    <h1 id="Title" class="text-4xl pt-6 pl-8 text-white max-w-[550px] overflow-hidden whitespace-nowrap text-ellipsis">
      Untitled
    </h1>
    <button @click="editTitle()" class="mb-2 ml-2">
      <img class="w-[25px] p-[3px] bg-white rounded-xl hover:bg-[#d6d6d6]" src="/src/assets/pencil-simple-line-bold.svg"
        alt="Rename">
    </button>
  </div>

  <main class="flex max-h-[95%]">
    <div v-for="list, index in lists" :key="index"
      class="p-[10px] mt-[120px] ml-[25px] bg-[#e4e4e4] text-black rounded-[5px] flex flex-col w-[250px] overflow-x-hidden">
      <div class="flex">
        <h1 id="cardTitle"
          class="pl-[5px] mb-0.5 text-[20px] max-w-[200px] overflow-hidden text-ellipsis whitespace-nowrap">
          Untitled</h1>
        <button class="ml-[2px] mb-0.5" @click="editCard">
          <img class="w-[20px] p-[2px] rounded hover:bg-[#d6d6d6]" alt="edit button"
            src="/src/assets/pencil-simple-line-bold.svg">
        </button>
      </div>

      <span id="cards" class="overflow-y-auto">
        <ul id="todo">
          <li class="bg-white rounded-[5px] shadow cursor-pointer mb-[10px]" v-for="(todo, index) in todos">
            <p class="whitespace-pre-line overflow-hidden text-ellipsis p-[10px] w-full text-[17px]"
              @click="todoDone(todo, index)">
              {{ todo }}</p>
          </li>
        </ul>

        <ul id="done">
          <li class="bg-[#d6d6d6] rounded-[5px] shadow cursor-pointer mb-[10px] select-none flex justify-between"
            v-for="(todo, index) in done">
            <p class="line-through whitespace-pre-line overflow-hidden text-ellipsis p-[10px] w-full text-[17px]"
              @click="todoUndo(todo, index)">{{ todo }}</p>
            <div class="flex justify-center items-center">
              <button class="rounded-lg hover:bg-[#f0ecec] w-[29px] mr-1" @click="todoDelete(index)">
                <img class="p-1.5" src="/src/assets/trash-bold.svg" alt="X">
              </button>
            </div>
          </li>
        </ul>
      </span>

      <div class="card flex justify-center w-full">
        <button v-if="addCardBtn" @click="show" class="addCard mt-1 w-full rounded-[5px] hover:bg-[#d6d6d6]">
          <div class="py-1 flex justify-center">
            <img class="w-[15px] mr-1" src="/src/assets/plus-bold.svg" alt="+">
            <p>Add a card</p>
          </div>
        </button>
      </div>
      <div class="flex justify-center">
        <div v-show="addCardTodo">
          <textarea
            class="p-[5px] rounded-[5px] bg-[#dadada] text-black w-[230px] outline-none resize-none text-[15px] mb-[3px] mt-[7px] hover:bg-white focus:bg-white"
            rows="2" col="0" type="text" placeholder="Enter a title for your card...   (Max 80 Characters)"
            v-model="newTodo" @keypress.enter="addNewTodo(); unShow()" maxlength="80"></textarea>
          <div class="flex justify-between">
            <button class="bg-[#6e87de] shadow text-white py-[2px] px-[7px] rounded-[10px] hover:bg-[#6074be]"
              @click="addNewTodo(); unShow();">Add card</button>
            <button @click="unShow" class="w-[30px] h-[30px] bg-white shadow rounded-[10px] hover:bg-[#e6e6e6]">
              <img class="p-1" src="/src/assets/x-bold.svg" alt="X">
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="ml-[25px] mt-[120px] text-black rounded-[5px] flex flex-col w-[205px]">
      <button id="button" class="p-[10px] rounded-[5px] bg-[#666666] text-black mr-[25px] cursor-not-allowed">
        <div class="flex justify-center">
          <img class="w-[15px] mr-1" src="/src/assets/plus-bold.svg" alt="+">
          <p>Add another list</p>
        </div>
      </button>
      <p class="text-gray-500 select-none">WIP</p>
    </div>
  </main>

  <span v-if="showCookie" class="z-10 fixed bg-slate-600 left-0 bottom-0 w-full text-white">
    <p class="p-3 pb-1">Trullu uses cookies to save your cards.</p>
    <button id="cookies" class="bg-white text-black px-2 py-1 ml-3 mb-3 rounded-lg hover:bg-gray-300"
      @click="setCookie">Accept</button>
  </span>
</template>