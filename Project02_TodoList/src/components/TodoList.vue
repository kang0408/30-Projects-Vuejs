<script setup>
import "./TodoList.scss";
import { ref, reactive } from "vue";

const inputTask = ref("");
const taskList = reactive([]);
const finishList = reactive([]);
const emptyTask = ref(true);
const checkedTask = ref(false);
const openHistory = ref(false);

function addTask() {
  if (inputTask.value == "") emptyTask.value = false;
  else {
    emptyTask.value = true;
    taskList.push(inputTask.value);
    saveData();
  }
  inputTask.value = "";
}

function finishTask(index) {
  finishList.push(taskList[index]);
  taskList.splice(index, 1);
  console.log(finishList);
  saveData();
}

function removeTask(index) {
  taskList.splice(index, 1);
  saveData();
}

function reverseTask(index) {
  taskList.push(finishList[index]);
  finishList.splice(index, 1);
  console.log(taskList);
}

function saveData() {
  localStorage.setItem("data-task-list", taskList);
  localStorage.setItem("data-finish-task-list", finishList);
}

function getData() {
  const dataTaskList = localStorage.getItem("data-task-list");
  if (dataTaskList !== null) {
    const localTaskList = dataTaskList.split(",");
    if (dataTaskList !== "") {
      localTaskList.forEach((item) => {
        taskList.push(item);
      });
    }
  }

  const dataFinishList = localStorage.getItem("data-finish-task-list");
  if (dataFinishList !== null) {
    const localFinishList = dataFinishList.split(",");
    if (dataFinishList !== "") {
      localFinishList.forEach((item) => {
        finishList.push(item);
      });
    }
  }
}

getData();
</script>
<template>
  <div :class="{ 'wrap-slide': openHistory }" class="wrap">
    <div class="icon--history" @click="openHistory = !openHistory">
      <i class="fa-solid fa-clock-rotate-left"></i>
    </div>
    <h1>To-do List</h1>
    <div class="create">
      <input
        v-model="inputTask"
        type="text"
        placeholder="Enter your task"
        @keypress.enter="addTask()"
        @focus="emptyTask = true"
      />
      <button @click="addTask()">Add</button>
    </div>
    <p v-if="!emptyTask">You must write something!</p>
    <ul class="todo-list">
      <li class="task" v-for="(item, index) in taskList" :key="index">
        <label class="item">
          <input @change="finishTask(index)" type="checkbox" />
          <span class="name">{{ item }}</span>
          <span class="checkmark"></span>
        </label>
        <span class="remove-task-icon" @click="removeTask(index)">
          <i class="fa-solid fa-xmark"></i>
        </span>
      </li>
    </ul>
    <div
      v-if="openHistory"
      :class="{ 'history-slide': openHistory }"
      class="history fade"
    >
      <ol class="history--list">
        <li class="task" v-for="(item, index) in finishList" :key="index">
          <span>
            {{ index + 1 }}.
            <span class="task-name">{{ item }}</span>
          </span>
          <span class="reverse-task-icon" @click="reverseTask(index)">
            <i class="fa-solid fa-rotate"></i>
          </span>
        </li>
      </ol>
    </div>
  </div>
</template>
