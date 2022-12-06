<template>
  <div style="display: flex; justify-content: center">
    <div
      v-for="(container, iContainer) in containers"
      class="container"
      :key="iContainer"
      @mouseenter="drop(iContainer)"
    >
      <p>Container {{ iContainer }}</p>
      <button @click="novoElemento(iContainer)" style="margin-bottom: 10px">
        Novo Elemento
      </button>
      <div
        class="card"
        v-for="(el, iEl) in container"
        :key="el"
        @mousedown="(ev) => dragMouseDown(ev, iContainer, iEl)"
      >
        {{ el }}
      </div>
    </div>
    <div>
      <button @click="novoContainer">Novo Container</button>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
const pos = reactive({
  pos1: 0,
  pos2: 0,
  pos3: 0,
  pos4: 0,
});
const containers = reactive([["teste"], []]);
const dragIn = ref(false);
const dragEl = reactive({
  list: "",
  el: -1,
});
function novoContainer() {
  containers.push([]);
}
function novoElemento(id) {
  containers[id].push(generateRandomString(5));
}
function drop(listN) {
  if (dragIn.value) {
    const del = containers[dragEl.list].splice(dragEl.el, 1);
    containers[listN].push(...del);
  }
}
function dragMouseDown(e, listName, ind) {
  e.preventDefault();
  dragEl.list = listName;
  dragEl.el = ind;
  dragIn.value = true;
  const elmnt = e.path[0];
  elmnt.style.position = "absolute";
  pos.pos4 = 0;
  // get the mouse cursor position at startup:
  pos.pos3 = e.clientX;
  pos.pos4 = e.clientY;
  document.onmouseup = closeDragElement;
  // call a function whenever the cursor moves:
  document.onmousemove = elementDrag;
}
function elementDrag(e) {
  const elmnt = e.path[0];
  e.preventDefault();

  // calculate the new cursor position:
  pos.pos1 = pos.pos3 - e.clientX;
  pos.pos2 = pos.pos4 - e.clientY;
  pos.pos3 = e.clientX;
  pos.pos4 = e.clientY;
  // set the element's new position:
  elmnt.style.top = elmnt.offsetTop - pos.pos2 + "px";
  elmnt.style.left = elmnt.offsetLeft - pos.pos1 + "px";
}

function closeDragElement(e) {
  pos.pos1 = 0;
  pos.pos2 = 0;
  pos.pos3 = 0;
  pos.pos4 = 0;
  setTimeout(() => {
    dragIn.value = false;
    dragEl.list = "";
    dragEl.el = "";
  });
  const elmnt = e.path[0];
  elmnt.style.top = "unset";
  elmnt.style.left = "unset";
  if (elmnt.style.position === "absolute") elmnt.style.position = "unset";
  /* stop moving when mouse button is released:*/
  document.onmouseup = null;
  document.onmousemove = null;
}
function generateRandomString(num) {
  let result1 = Math.random().toString(36).substring(0, num);

  return result1;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container {
  background-color: azure;
  padding: 15px;
  margin: 8px;
  max-width: 200px;
  min-height: 150px;
  min-width: 200px;
}
.card {
  cursor: move;
  background-color: white;
  border: 1px solid black;
  height: 50px;
  padding: 10px;
  max-width: 180px;
  min-width: 180px;
  margin-bottom: 8px;
}
</style>
