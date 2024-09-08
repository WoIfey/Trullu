<script setup>
import { ref, onMounted, watch } from 'vue'

const lists = ref([{ id: 1, title: "Untitled", todos: [], done: [] }])
const newTodo = ref('')
const addingCard = ref(null)
const mainTitle = ref('Untitled')

onMounted(() => {
  const savedLists = window.localStorage.getItem('lists')
  if (savedLists) {
    lists.value = JSON.parse(savedLists)
  }

  const savedTitle = window.localStorage.getItem('title')
  if (savedTitle) {
    mainTitle.value = savedTitle
    document.title = `${savedTitle} | Trullu`
  }
})

watch(lists, (newLists) => {
  window.localStorage.setItem('lists', JSON.stringify(newLists))
}, { deep: true })

function addNewTodo(listId) {
  if (!newTodo.value) return
  const list = lists.value.find(l => l.id === listId)
  if (list) {
    list.todos.push(newTodo.value)
    newTodo.value = ''
    addingCard.value = null
  }
}

function todoDone(listId, index) {
  const list = lists.value.find(l => l.id === listId)
  if (list) {
    const [todo] = list.todos.splice(index, 1)
    list.done.unshift(todo)
  }
}

function todoUndo(listId, index) {
  const list = lists.value.find(l => l.id === listId)
  if (list) {
    const [todo] = list.done.splice(index, 1)
    list.todos.push(todo)
  }
}

function todoDelete(listId, index) {
  const list = lists.value.find(l => l.id === listId)
  if (list) {
    list.done.splice(index, 1)
  }
}

function editCardTitle(listId) {
  const list = lists.value.find(l => l.id === listId)
  if (list) {
    let newTitle = prompt("Edit Card Title (Max 25 Characters)", list.title)
    if (newTitle) {
      list.title = newTitle.substring(0, 25)
    }
  }
}

function editTitle() {
  let newTitle = prompt("Rename Title (Max 30 Characters)", mainTitle.value)
  if (newTitle) {
    newTitle = newTitle.substring(0, 30)
    mainTitle.value = newTitle
    document.title = `${newTitle} | Trullu`
    window.localStorage.setItem("title", newTitle)
  }
}

function deleteAllCookies() {
  if (confirm("Do you want to delete all cards and cookies?")) {
    document.cookie.split(";").forEach(c => {
      document.cookie = c.replace(/^ +/, "").replace(/=.*/, "=;expires=" + new Date().toUTCString() + ";path=/")
    })
    window.localStorage.clear()
    lists.value = [{ id: 1, title: "Untitled", todos: [], done: [] }]
    mainTitle.value = "Untitled"
    document.title = "Untitled | Trullu"
    showCookie.value = true
  }
}

function addNewList() {
  lists.value.push({ id: Date.now(), title: "Untitled", todos: [], done: [] })
}

function removeList(listId) {
  if (confirm("Are you sure you want to remove this list?")) {
    lists.value = lists.value.filter(list => list.id !== listId)
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
      {{ mainTitle }}
    </h1>
    <button @click="editTitle" class="mb-2 ml-2">
      <img class="w-[25px] p-[3px] bg-white rounded-xl hover:bg-[#d6d6d6]" src="/src/assets/pencil-simple-line-bold.svg"
        alt="Rename">
    </button>
  </div>

  <main class="max-h-[85%] flex items-start mt-[125px]" :class="{ 'mr-[25px]': lists.length >= 10 }">
    <div v-for="list in lists" :key="list.id"
      class="p-[10px] ml-[25px] bg-[#e4e4e4] text-black rounded-[5px] flex flex-col w-[250px]">
      <div class="flex justify-between items-center">
        <div class="flex items-center">
          <h1 class="pl-[5px] mb-0.5 text-[20px] max-w-[170px] overflow-hidden text-ellipsis whitespace-nowrap">
            {{ list.title }}
          </h1>
          <button class="ml-[2px] mb-0.5" @click="editCardTitle(list.id)">
            <img class="w-[20px] p-[2px] rounded hover:bg-[#d6d6d6]" alt="edit button"
              src="/src/assets/pencil-simple-line-bold.svg">
          </button>
        </div>
        <button @click="removeList(list.id)">
          <img src="/src/assets/x-bold.svg" alt="Remove List" class="w-5 h-5">
        </button>
      </div>

      <span id="cards" class="overflow-y-auto">
        <ul id="todo">
          <li class="bg-white rounded-[5px] shadow cursor-pointer mb-[10px]" v-for="(todo, index) in list.todos"
            :key="index">
            <p class="whitespace-pre-line overflow-hidden text-ellipsis p-[10px] w-full text-[17px]"
              @click="todoDone(list.id, index)">
              {{ todo }}
            </p>
          </li>
        </ul>

        <ul id="done">
          <li class="bg-[#d6d6d6] rounded-[5px] shadow cursor-pointer mb-[10px] select-none flex justify-between"
            v-for="(todo, index) in list.done" :key="index">
            <p class="line-through whitespace-pre-line overflow-hidden text-ellipsis p-[10px] w-full text-[17px]"
              @click="todoUndo(list.id, index)">{{ todo }}</p>
            <div class="flex justify-center items-center">
              <button class="rounded-lg hover:bg-[#f0ecec] w-[29px] mr-1" @click="todoDelete(list.id, index)">
                <img class="p-1.5" src="/src/assets/trash-bold.svg" alt="X">
              </button>
            </div>
          </li>
        </ul>
      </span>

      <div class="flex justify-center w-full" :class="{ 'hidden': (list.todos.length + list.done.length) >= 12 }">
        <button v-if="addingCard !== list.id" @click="addingCard = list.id"
          class="mt-1 w-full rounded-[5px] hover:bg-[#d6d6d6]">
          <div class="py-1 flex justify-center">
            <img class="w-[15px] mr-1" src="/src/assets/plus-bold.svg" alt="+">
            <p>Add a card</p>
          </div>
        </button>
      </div>
      <div class="flex justify-center" v-if="addingCard === list.id">
        <div>
          <textarea
            class="p-[5px] rounded-[5px] bg-[#dadada] text-black w-[230px] outline-none resize-none text-[15px] mb-[3px] mt-[7px] hover:bg-white focus:bg-white"
            rows="2" col="0" type="text" placeholder="Enter a title for your card...   (Max 80 Characters)"
            v-model="newTodo" @keypress.enter="addNewTodo(list.id)" maxlength="80"></textarea>
          <div class="flex justify-between">
            <button class="bg-[#6e87de] shadow text-white py-[2px] px-[7px] rounded-[10px] hover:bg-[#6074be]"
              @click="addNewTodo(list.id)">Add card</button>
            <button @click="addingCard = null"
              class="w-[30px] h-[30px] bg-white shadow rounded-[10px] hover:bg-[#e6e6e6]">
              <img class="p-1" src="/src/assets/x-bold.svg" alt="X">
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="ml-[25px] text-black rounded-[5px] flex flex-col w-[205px]" :class="{ 'hidden': lists.length >= 10 }">
      <button id="button" @click="addNewList"
        class="p-[10px] rounded-[5px] bg-[#e4e4e4] hover:bg-[#d6d6d6] text-black mr-[25px]">
        <div class="flex justify-center">
          <img class="w-[15px] mr-1" src="/src/assets/plus-bold.svg" alt="+">
          <p>Add another list</p>
        </div>
      </button>
    </div>
  </main>
</template>