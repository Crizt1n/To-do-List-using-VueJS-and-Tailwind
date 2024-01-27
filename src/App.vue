<template>
  <!-- Main container for the app -->
  <main class="app bg-gray-100 min-h-screen p-8">
    <!-- Greeting section -->
    <section class="greeting bg-white p-6 mb-8 rounded-lg shadow-md">
      <!-- Greeting message with input for name -->
      <h2 class="title text-3xl mb-4">
        What's Up.! <strong><input type="text" placeholder="Your Name" v-model="name" class="ml-2 w-60 border-b-2 border-blue-500   text-blue-500 " /></strong>
      </h2>
    </section>

    <!-- Create To-Do section -->
    <section class="create-todo bg-white p-6 mb-8 rounded-lg shadow-md">
      <!-- Heading for creating a to-do list -->
      <h3 class="text-2xl mb-6">Create a To-Do List</h3>

      <!-- Form for adding a to-do item -->
      <form @submit.prevent="addTodo">
        <!-- Input for the to-do content -->
        <div class="mb-6">
          <label class="block text-sm font-semibold mb-2">What's on your to-do list?</label>
          <input
            type="text"
            placeholder="e.g., make a video"
            v-model="input_content"
            class="w-full border-b-2 border-gray-300 focus:outline-none"
          />
        </div>

        <!-- Radio buttons for selecting the category -->
        <div class="mb-6">
          <label class="block text-sm font-semibold mb-2">Pick a Category </label>
          <div class="flex items-center">
            <!-- Business category radio button -->
            <label class="mr-6">
              <input
                type="radio"
                name="category"
                value="business"
                v-model="input_category"
                class="mr-2 text-blue-500"
              />
              Business
            </label>

            <!-- Personal category radio button -->
            <label>
              <input
                type="radio"
                name="category"
                value="personal"
                v-model="input_category"
                class="mr-2 text-green-500"
              />
              Personal
            </label>
          </div>
        </div>

        <!-- Button to add the to-do item -->
        <button type="submit" class="bg-blue-500 text-white py-3 px-6 rounded hover:bg-blue-600">Add Todo</button>
      </form>
    </section>

    <!-- To-Do List section -->
    <section class="todo-list bg-white p-6 rounded-lg shadow-md">
      <!-- Heading for the to-do list -->
      <h3 class="text-2xl mb-6">To-Do List</h3>
      <!-- List of to-do items -->
      <div class="list">
        <!-- Loop through and display each to-do item -->
        <div
          v-for="todo in sortedTodos"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'} flex items-center mb-6`"
        >
          <!-- Checkbox for marking the to-do item as done -->
          <label class="mr-6">
            <input type="checkbox" v-model="todo.done" class="mr-2 text-blue-500" />
            <!-- Colored bubble indicating the category -->
            <span :class="`bubble ${todo.category} inline-block w-5 h-5 rounded-full`"></span>
          </label>
          <!-- Input for editing the to-do item content -->
          <div class="todo-content flex-grow">
            <input type="text" v-model="todo.content" class="w-full border-b-2 border-gray-300 focus:outline-none" />
          </div>
          <!-- Button to delete the to-do item -->
          <div class="actions">
            <button class="delete text-red-500 hover:text-red-700" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script>
import { ref, onMounted, computed, watch } from 'vue';

export default {
  name: 'App',
  setup() {
    // Define reactive variables
    const todos = ref([]);
    const name = ref('');
    const input_content = ref('');
    const input_category = ref(null);

    // Computed property for sorted todos
    const sortedTodos = computed(() =>
      todos.value.slice().sort((a, b) => b.createdAt - a.createdAt)
    );

    // Function to add a new to-do item
    const addTodo = () => {
      if (input_content.value.trim() === '' || input_category.value === null) {
        return;
      }
      todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        createdAt: new Date().getTime(),
      });

      // Clear input fields after adding todo
      input_content.value = '';
      input_category.value = null;
    };

    // Function to remove a to-do item
    const removeTodo = (todo) => {
      todos.value = todos.value.filter((t) => t !== todo);
    };

    // Watch for changes in todos and update local storage
    watch(todos, (newVal) => {
      localStorage.setItem('todos', JSON.stringify(newVal));
    }, { deep: true });

    // Watch for changes in name and update local storage
    watch(name, (newVal) => {
      localStorage.setItem('name', newVal);
    });

    // Load data from local storage on component mount
    onMounted(() => {
      name.value = localStorage.getItem('name') || '';
      todos.value = JSON.parse(localStorage.getItem('todos')) || [];
    });

    // Return variables and functions for the component
    return {
      name,
      input_content,
      input_category,
      sortedTodos,
      addTodo,
      removeTodo,
    };
  },
};
</script>

<style>
/* Add your styles here */
</style>
