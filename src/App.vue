<script setup lang="ts">
import { ref, watch, onMounted } from "vue";

interface ToDo {
  content: string;
  category: string;
  done: boolean;
  createdAt: number;
}

const todos = ref<ToDo[]>([]);
const todosOrdererd = () => {
  return todos.value.slice().reverse();
};
const name = ref("");
const content_error = ref("");
const category_error = ref("");

const input_content = ref("");
const input_category = ref("");

const addToDo = () => {
  content_error.value = "";
  category_error.value = "";
  if (input_content.value.trim() === "") {
    content_error.value = "Please enter some content";
    return;
  }

  if (input_category.value === "") {
    category_error.value = "Please pick a category";
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: Date.now(),
  });
};

const removeToDo = (todo: ToDo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || "");
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Whats up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREAT A TODO</h3>
      <form @submit.prevent="addToDo">
        <h4>What's on your todo lsit? {{ content_error }}</h4>
        <input
          type="text"
          placeholder="e.g. make a video"
          v-model="input_content"
        />

        <h4>Pick a category. {{ category_error }}</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="buisness"
              v-model="input_category"
            />
            <span class="bubble buisness"></span>
            <div>Buisness</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
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
        <div
          v-for="todo in todosOrdererd()"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeToDo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
