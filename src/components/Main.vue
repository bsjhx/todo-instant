<template>
  <div class="buttons-section">
    <button
        class="pure-button pure-button-primary"
        @click="createTodoList"
        v-if="todos.length === 0"
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

  <form class="pure-form">
    <label v-if="todos.length === 0">
    <textarea
        v-model="text"
        class="pure-input textarea-class"
    ></textarea>
    </label>
  </form>

  <div v-if="todos[0]">
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
      todos: []
    }
  },
  methods: {
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
