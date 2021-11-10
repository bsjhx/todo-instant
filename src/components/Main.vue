<template>
  <div class="buttons-section">
    <button
        class="pure-button pure-button-primary"
        @click="addNewTodoList"
        v-if="mode === modes.LIST_ALL_TODO_LISTS">
      Add new todo list
    </button>
    <button
        class="pure-button pure-button-primary"
        @click="createTodoList"
        v-if="mode === modes.ADD_NEW"
        :disabled="text.length === 0">
      Create todo list
    </button>
    <button
        class="pure-button pure-button-primary"
        @click="reset"
        v-if="todos.length > 0">
      Reset
    </button>
  </div>

  <!-- all lists -->
  <div v-if="mode === modes.LIST_ALL_TODO_LISTS">
    <div v-for="todoList in todos" :key="todoList.id">
      <p>{{ todoList.id }}</p>
      <p>{{ todoList.elements }}</p>
    </div>
  </div>

  <!-- new list editor -->
  <form class="pure-form">
    <label v-if="mode === modes.ADD_NEW">
    <textarea
        v-model="text"
        class="pure-input textarea-class"
    ></textarea>
    </label>
  </form>

  <!-- single to do list -->
  <div v-if="mode === modes.TODO_LIST_DETAILS">
    <div v-for="todo in todos[0].elements"
         :key="todo.title"
         @click="changeTodoElementState(todo)"
         class="todoList"
    >
      <div v-if="!todo.isDone" class="element element-todo">{{ todo.title }}</div>
      <div v-if="todo.isDone" class="element element-done">{{ todo.title }}</div>
    </div>
  </div>

</template>

<script>
import Modes from "./modes";

function generateId() {
  let result = '';
  const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  const charactersLength = characters.length;
  for (let i = 0; i < 20; i++) {
    result += characters.charAt(Math.floor(Math.random() *
        charactersLength));
  }
  return result;
}

export default {
  name: 'HelloWorld',
  data: function () {
    return {
      text: '',
      todos: [],
      selectedTodolistId: '',
      modes: Modes,
      mode: ''
    }
  },
  methods: {
    addNewTodoList() {
      this.mode = this.modes.ADD_NEW
    },
    createTodoList() {
      const id = generateId()
      const newTodos = {}
      newTodos.id = id
      newTodos.elements = []
      this.text.split(/\r?\n/)
          .filter(s => s.length > 0)
          .map(s => newTodos.elements.push({isDone: false, title: s}))

      this.todos.push(newTodos)
      localStorage.setItem('todos', JSON.stringify(this.todos))

      this.mode = this.modes.LIST_ALL_TODO_LISTS
      this.text = ''
    },
    findTodoLisById(id) {
      return this.todos.find(value => value.id === id)
    },
    reset() {
      this.text = ''
      localStorage.setItem('todos', '')
      this.todos = []
    },
    changeTodoElementState(todo) {
      todo.isDone = !todo.isDone
      localStorage.setItem('todos', JSON.stringify(this.todos))
    }
  },
  mounted() {
    const storedText = localStorage.getItem('todos')
    this.mode = this.modes.LIST_ALL_TODO_LISTS
    console.log(this.modes, this.mode)
    if (storedText) {
      this.text = storedText
      this.todos = JSON.parse(storedText)
    }
  }
}

</script>

<style scoped>
.todoList {
  cursor: pointer
}

.element {
  padding-bottom: 20px;
  padding-top: 20px;
}

.element-done {
  text-decoration: line-through;
  background-color: #7bcf74;
}

.element-todo {
}

.buttons-section {
  padding-bottom: 20px;
  padding-top: 20px;
  background-color: #7bcf74;
}

.textarea-class {
  margin-top: 20px;
  width: 80%;
  height: 540px;
}
</style>
