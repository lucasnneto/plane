<template>
  <div>
    <div style="display: flex; justify-content: center">
      <div
        v-for="(container, iContainer) in containers"
        class="container"
        :key="iContainer"
        @drop="(ev) => drop(ev, iContainer)"
        @dragover="allowDrop"
      >
        <p>Container {{ iContainer }}</p>
        <div
          class="card"
          v-for="(el, iEl) in container"
          :key="el"
          @dragstart="(ev) => drag(ev, iEl, iContainer)"
          draggable="true"
        >
          {{ el }}
        </div>
        <button @click="novoElemento(iContainer)">Novo Elemento</button>
      </div>
      <div class="container">
        <button @click="novoContainer">Novo Container</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive } from "vue";

const containers = reactive([["teste"], []]);
const inMove = reactive({
  inMove: false,
  container: -1,
  value: "",
  valueid: -1,
});
function novoContainer() {
  containers.push([]);
}
function novoElemento(id) {
  containers[id].push(generateRandomString(5));
}
function drag(ev, iEl, iContainer) {
  moveIt(true, iContainer, iEl);
  ev.dataTransfer.setData("Text", JSON.stringify({ iEl, iContainer }));
}
function drop(e, iContainerAtual) {
  cleanMoveIt();
  var data = JSON.parse(e.dataTransfer.getData("Text"));
  const del = containers[data.iContainer].splice(data.iEl, 1);
  containers[iContainerAtual].push(...del);
}
function allowDrop(ev) {
  ev.preventDefault();
}
function moveIt(move, iContainer, iEl) {
  inMove.inMove = move;
  inMove.container = iContainer;
  inMove.value = containers[iEl];
  inMove.valueid = iEl;
}
function cleanMoveIt() {
  inMove.inMove = false;
  inMove.container = -1;
  inMove.value = "";
  inMove.valueid = -1;
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
  min-width: 200px;
}
.card {
  cursor: move;
  background-color: white;
  border: 1px solid black;
  height: 50px;
  padding: 10px;
  margin-bottom: 8px;
}
</style>
