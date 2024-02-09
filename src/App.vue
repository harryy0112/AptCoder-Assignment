<template>
  <div id="app">
    <h1>To-do List</h1>
    <form @submit.prevent="addOrUpdateTodo">
      <input type="text" v-model="newTodo" placeholder="Add a todo" />
      <button v-if="editTodo">{{ editTodo ? "Update" : "Add" }}</button>
    </form>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        <input type="checkbox" v-model="todo.done" @change="updateTodo(todo)" />
        <span :class="{ done: todo.done }">{{ todo.title }}</span>
        <span>{{ todo.date }}</span>
        <button @click="startEditing(todo)">Edit</button>
        <button @click="deleteTodo(todo)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      todos: [],
      newTodo: "",
      editTodo: null,
    };
  },
  async created() {
    const response = await axios.get("http://localhost:3000/todos");
    this.todos = response.data;
  },
  methods: {
    async addOrUpdateTodo() {
      if (this.editTodo) {
        this.editTodo.title = this.newTodo;
        await this.updateTodo(this.editTodo);
        this.editTodo = null;
      } else {
        const todo = {
          title: this.newTodo,
          done: false,
          date: new Date().toLocaleDateString(),
        };
        const response = await axios.post("http://localhost:3000/todos", todo);
        this.todos.push(response.data);
      }
      this.newTodo = "";
    },
    async updateTodo(todo) {
      await axios.put(`http://localhost:3000/todos/${todo.id}`, todo);
    },
    startEditing(todo) {
      this.newTodo = todo.title;
      this.editTodo = todo;
    },
    async deleteTodo(todo) {
      await axios.delete(`http://localhost:3000/todos/${todo.id}`);
      this.todos = this.todos.filter((t) => t.id !== todo.id);
    },
  },
};
</script>

<style scoped>
#app {
  font-family: "Arial", sans-serif;
  max-width: 600px;
  margin: 0 auto;
  padding: 1em;
  background-color: #f9f9f9;
  border-radius: 4px;
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
  font-size: 2em;
  margin-bottom: 0.5em;
}

form {
  display: flex;
  justify-content: center;
  margin-bottom: 1em;
}

input[type="text"] {
  flex-grow: 1;
  padding: 0.5em;
  border: 1px solid #ddd;
  border-radius: 2px;
  font-size: 1em;
}

button {
  margin-left: 0.5em;
  padding: 0.5em 1em;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 2px;
  cursor: pointer;
  font-size: 1em;
}

button:hover {
  background-color: #0056b3;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1em; /* Adjust this to change the internal spacing */
  margin: 0.5em 0; /* Adjust this to change the external spacing */
  border-bottom: 1px solid #ddd;
}

li > span {
  flex: 1;
  margin-right: 0.5em;
  text-align: justify;
  font-size: 1.2em;
}

input[type="checkbox"] {
  margin-right: 0.5em;
  transform: scale(1.2);
}

.done {
  text-decoration: line-through;
  color: #888;
}
</style>
