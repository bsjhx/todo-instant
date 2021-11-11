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
        @click="cancel"
        v-if="mode === modes.ADD_NEW || mode === modes.TODO_LIST_DETAILS">
      {{ mode === modes.TODO_LIST_DETAILS ? 'Back' : 'Cancel' }}
    </button>
  </div>

  <!-- new list editor -->
  <div v-if="mode === modes.ADD_NEW">
    <form class="pure-form">
      <label>
      <textarea
          v-model="text"
          class="pure-input textarea-class"
      ></textarea>
      </label>
    </form>
  </div>

  <!-- all lists -->
  <div v-if="mode === modes.LIST_ALL_TODO_LISTS">
    <div
        v-for="(todoList, index) in todos"
        :key="todoList.id"
        class="all-list"
        @click="showList(todoList)">
      <div class="element-all-list" :class="{'element-all-list-even' : index % 2 === 1}">{{ todoList.id }}</div>
    </div>
  </div>

  <!-- single to do list -->
  <div v-if="mode === modes.TODO_LIST_DETAILS">
    <div v-for="todo in selectedTodoList.elements"
         :key="todo.title"
         @click="changeTodoElementState(todo)"
         class="todoList"
    >
      <div v-if="!todo.isDone" class="element element-todo">{{ todo.title }}</div>
      <div v-if="todo.isDone" class="element element-done">{{ todo.title }}</div>
    </div>
  </div>

  <div class="version">
    {{ version }}
  </div>

</template>

<script>
import Modes from "./modes";
import {version} from '../../package.json'

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
      selectedTodoList: {},
      modes: Modes,
      mode: '',
      version: version
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
    cancel() {
      this.text = ''
      this.mode = this.modes.LIST_ALL_TODO_LISTS
    },
    showList(todoList) {
      this.selectedTodoList = todoList
      this.mode = this.modes.TODO_LIST_DETAILS
    },
    changeTodoElementState(todo) {
      todo.isDone = !todo.isDone
      localStorage.setItem('todos', JSON.stringify(this.todos))
    }
  },
  mounted() {
    const storedText = localStorage.getItem('todos')
    this.mode = this.modes.LIST_ALL_TODO_LISTS
    if (storedText) {
      this.todos = JSON.parse(storedText)
    }
  }
}

</script>

<style scoped>
.all-list {
  cursor: pointer;
}

.element-all-list {
  padding-bottom: 40px;
  padding-top: 40px;
  background-color: #F5F5F6;
}

.element-all-list-even {
  background-color: #cfe6cf;
}

.todoList {
  cursor: pointer
}

.element {
  padding-bottom: 20px;
  padding-top: 20px;
}

.element-done {
  text-decoration: line-through;
  background-color: #60ad5e;
}

.element-todo {
}

.buttons-section {
  padding-bottom: 20px;
  padding-top: 20px;
  background-color: #2e7d32;
}

.textarea-class {
  margin-top: 20px;
  width: 80%;
  height: 540px;
}

.version {
  position: fixed;
  bottom: 0;
  right: 0;
  margin-right: 20px;
  margin-bottom: 5px;
}
</style>
