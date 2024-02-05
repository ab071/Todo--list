<template>
  <div class="container mx-auto px-10 md:px-0">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <div></div>
      <div>
        <div class="mt-10">
          <h1 class="text-5xl font-semibold text-blue-900">Liste de tâches</h1>
          <div class="mt-6">
            <form @submit.prevent="addTodo">
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
          <div class="filter-options mt-4 text-center">
            <button class="filter-btn mr-2" @click="setFilter('all')">Toutes</button>
            <button class="filter-btn mr-2" @click="setFilter('completed')">Complétées</button>
            <button class="filter-btn" @click="setFilter('active')">Actives</button>
          </div>
          <div class="mt-8">
            <div id="todoList">
              <transition-group name="fade" tag="div" class="grid grid-cols-1 gap-3">
                <div v-if="filteredTodos.length === 0" key="empty">
                  <p class="text-gray-400">Aucune tâche ici !</p>
                </div>
                <div
                  v-for="(todo, index) in filteredTodos"
                  :key="todo.id"
                  class="bg-white rounded shadow-md p-3 flex flex-col"
                >
                  <div :class="{ 'completed': todo.complete }" class="mb-2">
                    <input
                      v-if="todo.editing"
                      v-model="todo.text"
                      @keyup.enter="finishEdit(todo)"
                      @blur="finishEdit(todo)"
                      class="text-md outline-none w-full"
                    />
                    <span v-else>{{ todo.text }}</span>
                  </div>
                  <div class="flex justify-between">
                    <button class="text-red-400 hover:text-red-600 font-semibold" @click="removeTodo(index)">Supprimer</button>
                    <button v-if="!todo.editing" class="text-blue-400 hover:text-blue-600 font-semibold" @click="startEdit(todo)">Modifier</button>
                    <button v-else class="text-green-400 hover:text-green-600 font-semibold" @click="finishEdit(todo)">Terminer</button>
                    <button class="text-green-400 hover:text-green-600 font-semibold" @click="toggleComplete(index)">
                      {{ todo.complete ? 'Marquer non complétée' : 'Marquer complétée' }}
                    </button>
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
import { ref, computed } from "vue";

const newTodo = ref("");
const todos = ref(JSON.parse(localStorage.getItem("todos")) || []);
const filter = ref('all');

function addTodo() {
  if (newTodo.value.trim() !== "") {
    todos.value.push({ id: Date.now(), text: newTodo.value, complete: false, editing: false });
    newTodo.value = "";
    updateStorage();
  }
}

function removeAllTodos() {
  todos.value = [];
  updateStorage();
}

function removeTodo(index) {
  todos.value.splice(index, 1);
  updateStorage();
}

function toggleComplete(index) {
  todos.value[index].complete = !todos.value[index].complete;
  updateStorage();
}

function startEdit(todo) {
  todo.editing = true;
}

function finishEdit(todo) {
  if (!todo.text.trim()) {
    todos.value.splice(todos.value.indexOf(todo), 1);
  } else {
    todo.editing = false;
  }
  updateStorage();
}

function setFilter(f) {
  filter.value = f;
}

const filteredTodos = computed(() => {
  return todos.value.filter(todo => {
    switch (filter.value) {
      case 'completed': return todo.complete;
      case 'active': return !todo.complete;
      default: return true;
    }
  });
});

function updateStorage() {
  localStorage.setItem("todos", JSON.stringify(todos.value));
}
</script>

<style scoped>
.completed {
  text-decoration: line-through;
  color: #a0aec0;
}
.filter-btn {
  background-color: #f7fafc;
  border: 1px solid #cbd5e0;
  color: #4a5568;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
}
.filter-btn:hover {
  background-color: #edf2f7;
}
</style>
