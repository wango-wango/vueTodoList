<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

//運作的function
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  console.log("addTodo");
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    // 做排序用
    createdAt: new Date().getTime(),
  });

  input_content.value = '';
  input_category.value = null;
};

// 刪除
const removeTodo = (todo) => {
  todos.value = todos.value.filter((v) => v !== todo);
};

// 排序
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

// 監聽
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

// 監聽
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
// 生命週期的概念
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Whats up,
        <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a todo</h3>
      <form id="new_todo_form" @submit.prevent="addTodo">
        <h4>What is on your todo list ?</h4>
        <input type="text" placeholder="e.g. make a video" v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
