<script setup>
const title = document.querySelector("#Title")
function savedTitle() {
  let titleH = localStorage.getItem("title");
  document.title = titleH + " | Trullu"
  console.log(titleH)
  title.textContent = titleH
  if (titleH === null) {
    title.innerText = "Untitled"
    document.title = "Untitled | Trullu"
  }
}

function rename() {
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
savedTitle()
</script>

<template>
  <button @click="rename()">
    <img src="/src/assets/pencil-simple-line-bold.svg" alt="Rename">
  </button>
</template>

<style scoped>
img {
  width: 25px;
  padding: 3px;
  background: white;
  border-radius: 50%;
}
</style>