<template>
  <div class="buttons-section">
    <button
        class="button-success pure-button"
        @click="addNewTodoList"
        v-if="mode === modes.LIST_ALL_TODO_LISTS">
      Add new todo list
    </button>
    <button
        class="button-success pure-button"
        @click="createTodoList"
        v-if="mode === modes.ADD_NEW"
        :disabled="todoListElementsText.length === 0">
      Create todo list
    </button>
    <button
        class="button-success pure-button"
        @click="editList()"
        v-if="mode === modes.TODO_LIST_DETAILS">
      Edit list
    </button>
    <button
        class="button-error pure-button"
        @click="removeList(selectedTodoList.id)"
        v-if="mode === modes.TODO_LIST_DETAILS">
      Remove list
    </button>
    <button
        class="button-success pure-button"
        @click="updateList()"
        v-if="mode === modes.EDIT_LIST"
        :disabled="todoListElementsText.length === 0">
      Save changes
    </button>
    <button
        class="button-warning pure-button"
        @click="cancel(selectedTodoList.id)"
        v-if="mode === modes.ADD_NEW || mode === modes.TODO_LIST_DETAILS || mode === modes.EDIT_LIST">
      {{ mode === modes.TODO_LIST_DETAILS ? 'Back' : 'Cancel' }}
    </button>
  </div>

  <!-- new list / edit list -->
  <div v-if="mode === modes.ADD_NEW || mode === modes.EDIT_LIST">
    <form class="pure-form">
      <div class="pure-control-group form-control-group-general">
        <label for="todo-list-name" class="form-label-general">Name</label>
        <input
            type="text"
            id="todo-list-name"
            placeholder="Name"
            class="pure-input form-text-input-general"
            v-model="todoListName"/>
      </div>
      <div class="pure-control-group form-control-group-general">
        <label for="todo-list-elements" class="form-label-general">Tasks</label>
        <textarea
            id="todo-list-elements"
            v-model="todoListElementsText"
            class="pure-input form-text-input-general form-text-area"
        ></textarea>
      </div>
    </form>
  </div>

  <!-- all lists -->
  <div v-if="mode === modes.LIST_ALL_TODO_LISTS">
    <div
        v-for="(todoList, index) in todos"
        :key="todoList.id"
        class="all-list"
        @click="showList(todoList)">
      <div class="element-all-list" :class="{'element-all-list-even' : index % 2 === 1}">
        {{ todoList.name ? todoList.name : todoList.id }}
      </div>
    </div>
  </div>

  <!-- single to do list -->
  <div v-if="mode === modes.TODO_LIST_DETAILS">
    <div class="todo-list-name">{{ selectedTodoList.name }}</div>
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
      todoListElementsText: '',
      todoListName: '',
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
      const newTodosList = {}
      newTodosList.id = id
      newTodosList.name = this.todoListName ? this.todoListName : id
      newTodosList.elements = []
      this.todoListElementsText.split(/\r?\n/)
          .filter(s => s.length > 0)
          .map(s => newTodosList.elements.push({isDone: false, title: s}))

      this.todos.push(newTodosList)
      localStorage.setItem('todos', JSON.stringify(this.todos))

      this.selectedTodoList = newTodosList
      this.mode = this.modes.TODO_LIST_DETAILS
      this.todoListElementsText = ''
      this.todoListName = ''
    },
    cancel(id) {
      this.todoListElementsText = ''
      this.todoListName = ''
      if (id && this.mode === this.modes.EDIT_LIST) {
        this.mode = this.modes.TODO_LIST_DETAILS
      } else {
        this.mode = this.modes.LIST_ALL_TODO_LISTS
      }
    },
    removeList(id) {
      this.todos = this.todos.filter(el => el.id !== id)
      localStorage.setItem('todos', JSON.stringify(this.todos))
      this.selectedTodoList = {}
      this.mode = this.modes.LIST_ALL_TODO_LISTS
    },
    editList() {
      this.mode = this.modes.EDIT_LIST
      this.todoListElementsText = this.selectedTodoList.elements.map(el => el.title).join('\n')
      this.todoListName = this.selectedTodoList.name
    },
    updateList() {
      this.selectedTodoList.name = this.todoListName ? this.todoListName : this.selectedTodoList.id
      this.selectedTodoList.elements = []
      this.todoListElementsText.split(/\r?\n/)
          .filter(s => s.length > 0)
          .map(s => this.selectedTodoList.elements.push({isDone: false, title: s}))

      localStorage.setItem('todos', JSON.stringify(this.todos))

      this.mode = this.modes.TODO_LIST_DETAILS
      this.todoListElementsText = ''
      this.todoListName = ''
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
  background-color: #cfe6cf;
}

.element-todo {
}

.todo-list-name {
  padding-top: 20px;
  padding-bottom: 20px;
  text-transform: uppercase;
  font-size: 30px;
  font-weight: bold;
  background-color: #60ad5e;
}

.buttons-section {
  padding-bottom: 20px;
  padding-top: 20px;
  background-color: #2e7d32;
}

.button-success,
.button-error,
.button-warning {
  color: white;
  border-radius: 4px;
  text-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
  margin-right: 20px;
}

.button-success {
  background: rgb(28, 184, 65);
}

.button-error {
  background: rgb(202, 60, 60);
}

.button-warning {
  background: rgb(223, 117, 20);
}

.form-control-group-general {
  margin-top: 20px;
}

.form-text-input-general {
  width: 80%;
  margin: auto;
}

.form-label-general {
  margin-right: 10px;
}

.form-text-area {
  height: 500px;
}

.version {
  position: fixed;
  bottom: 0;
  right: 0;
  margin-right: 20px;
  margin-bottom: 5px;
}
</style>
