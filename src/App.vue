<template>
  <div class="container mx-auto px-10 md:px-0">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <div></div>
      <div>
        <div class="mt-10">
          <h1 class="text-5xl font-semibold text-blue-400"> liste de tâches</h1>

          <div class="mt-6">
            <form @submit.prevent="addTodo()">
              <div class="grid grid-cols-1 gap-4">
                <div>
                  <input class="border-2 outline-none py-2 px-2 shadow-md font-medium w-full rounded border-blue-300 hover:border-blue-600 focus:border-blue-600 focus:shadow-blue-200" v-model="newTodo" />
                </div>
                <div>
                  <div class="grid grid-cols-2 gap-2">
                    <div>
                      <button class="text-white py-2 px-4 shadow-md w-full rounded bg-red-400 hover:bg-red-600 font-semibold" v-if="todos.length !== 0" @click="removeAllTodos">Spprimmer tout</button>
                    </div>
                    <div>
                      <button class="text-white py-2 px-4 shadow-md w-full rounded bg-blue-400 hover:bg-blue-600 font-semibold">Ajoutez la liste</button>
                    </div>
                  </div>
                </div>
              </div>
            </form>
          </div>

          <div class="mt-8 text-center">
            <div id="todoList">
              <transition-group name="fade" tag="div" class="grid grid-cols-1 gap-3">
                <div v-if="todos.length === 0" key="empty">
                  <p class="text-gray-400">la liste est vide ! </p>
                </div>
                <div
                  class="rounded shadow-md p-3 h-full hover:shadow-gray-400 text-gray-600 text-lg font-semibold"
                  :class="{ completed: todo.complete, editing: editingTodoIndex === index }"
                  v-for="(todo, index) in todos"
                  :key="index"
                  @click="completedTodo(todo, index)"
                  ref="todoItems"
                >
                  <span v-if="editingTodoIndex !== index" class="text-gray-600 text-lg font-semibold">{{ todo.text }}</span>
                  <input
                    v-else
                    class="border-2 outline-none py-2 px-2 shadow-md font-medium w-full rounded border-blue-300 hover:border-blue-600 focus:border-blue-600 focus:shadow-blue-200"
                    v-model="todos[index].text"
                    @blur="updateTodo(index)"
                    @keyup.enter="updateTodo(index)"
                  />
                  <button
                    v-if="editingTodoIndex !== index"
                    class="text-green-600 hover:text-red-600 font-semibold ml-2"
                    @click.stop="editTodo(index)"
                  >Edit</button>
                </div>
              </transition-group>
            </div>
          </div>
        </div>
      </div>
      <div></div>
    </div>
  </div>
</template>

<script setup>
  import { ref } from "vue";
  import { gsap } from "gsap";

  const newTodo = ref("");
  const editingTodoIndex = ref(-1);

  let storedTodos;
  localStorage.getItem("todos") ? (storedTodos = JSON.parse(localStorage.getItem("todos"))) : (storedTodos = []);

  const todos = ref(storedTodos);

  function addTodo() {
    if (newTodo.value !== "") {
      todos.value.push({ complete: false, text: newTodo.value });
      newTodo.value = "";
    }
  }

  function removeAllTodos() {
    todos.value.splice(0, todos.value.length);
    updateStorage();
  }

  function completedTodo(todo, index) {
    todo.complete = !todo.complete;
    const item = this.$refs.todoItems[index];
    gsap.to(item, { opacity: 0, duration: 0.5, onComplete: () => {
      updateStorage();
      gsap.to(item, { opacity: 1, duration: 0.5 });
    }});
  }

  function editTodo(index) {
    editingTodoIndex.value = index;
  }
// eslint-disable-next-line no-unused-vars
  function updateTodo(todo, index) {
    editingTodoIndex.value = -1;
    updateStorage();
  }

  function updateStorage() {
    localStorage.setItem("todos", JSON.stringify(todos.value));
  }
</script>

<style>
  .completed {
    text-decoration: line-through;
  }
  .editing {
    background-color: #E6E6E6;
  }
</style>

