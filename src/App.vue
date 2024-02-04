<template>
  <div class="container mx-auto px-10 md:px-0">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <div></div>
      <div>
        <div class="mt-10">
          <h1 class="text-5xl font-semibold text-blue-900">Liste de tâches</h1>

          <div class="mt-6">
            <form @submit.prevent="addTodo()">
              <div class="grid grid-cols-1 gap-4">
                <div>
                  <input
                    type="text"
                    class="border-2 outline-none py-2 px-2 shadow-md font-medium w-full rounded border-blue-300 hover:border-blue-600 focus:border-blue-600 focus:shadow-blue-200"
                    placeholder="Ajoutez une nouvelle tâche ici"
                    v-model="newTodo"
                  />
                </div>
                <div>
                  <div class="grid grid-cols-2 gap-2">
                    <div>
                      <button
                        type="button"
                        class="text-white py-2 px-4 shadow-md w-full rounded bg-red-400 hover:bg-red-600 font-semibold"
                        v-if="todos.length"
                        @click="removeAllTodos"
                      >
                        Supprimer tout
                      </button>
                    </div>
                    <div>
                      <button
                        type="submit"
                        class="text-white py-2 px-4 shadow-md w-full rounded bg-blue-400 hover:bg-blue-600 font-semibold"
                      >
                        Ajouter la tâche
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </form>
          </div>

          <div class="mt-8">
            <div id="todoList">
              <transition-group name="fade" tag="div" class="grid grid-cols-1 gap-3">
                <div v-if="todos.length === 0" key="empty">
                  <p class="text-gray-400">La liste est vide !</p>
                </div>
                <div
                  v-for="(todo, index) in todos"
                  :key="todo.id"
                  class="bg-white rounded shadow-md p-3 flex flex-col"
                >
                  <div :class="{ 'completed': todo.complete }" class="mb-2">{{ todo.text }}</div>
                  <div class="flex justify-between">
                    <button v-if="editingTodoIndex !== index" class="text-red-400 hover:text-red-600 font-semibold" @click="removeTodo(index)">Delete</button>
                    <button v-if="editingTodoIndex !== index" class="text-blue-400 hover:text-blue-600 font-semibold" @click="editTodo(index)">Edit</button>
                  </div>
                  <div v-show="editingTodoIndex === index" class="mt-4 flex flex-col">
                    <input
                      type="text"
                      class="border-2 outline-none py-2 px-2 shadow-md font-medium w-full rounded border-blue-300 hover:border-blue-600 focus:border-blue-600 focus:shadow-blue-200 mb-1"
                      v-model="editTodoText"
                    />
                    <div class="flex justify-between">
                      <button class="text-green-400 hover:text-green-600 font-semibold" @click="updateTodo(index)">Save</button>
                      <button class="text-red-400 hover:text-red-600 font-semibold" @click="cancelEdit">Cancel</button>
                    </div>
                  </div>
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

const newTodo = ref("");
const editingTodoIndex = ref(-1);
const editTodoText = ref("");

let storedTodos;
localStorage.getItem("todos") ? (storedTodos = JSON.parse(localStorage.getItem("todos"))) : (storedTodos = []);

const todos = ref(storedTodos.map((todo, index) => ({ ...todo, id: index })));

function addTodo() {
  if (newTodo.value !== "") {
    todos.value.push({ complete: false, text: newTodo.value });
    newTodo.value = "";
    updateStorage();
  }
}

function removeAllTodos() {
  todos.value.splice(0, todos.value.length);
  updateStorage();
}

function removeTodo(index) {
  todos.value.splice(index, 1);
  updateStorage();
}

function editTodo(index) {
  editingTodoIndex.value = index;
  editTodoText.value = todos.value[index].text;
}

function cancelEdit() {
  editingTodoIndex.value = -1;
}

function updateTodo(index) {
  todos.value[index].text = editTodoText.value;
  editingTodoIndex.value = -1;
  updateStorage();
}

function updateStorage() {
  localStorage.setItem("todos", JSON.stringify(todos.value));
}
</script>

<style scoped>
.completed {
  text-decoration: line-through;
}
</style>
