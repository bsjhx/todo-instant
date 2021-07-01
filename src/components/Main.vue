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

  <div v-for="todo in todos"
       :key="todo.title"
       @click="todo.isDone = !todo.isDone"
       class="todoList"
  >
    <div v-if="!todo.isDone" class="element element-todo">{{ todo.title }}</div>
    <div v-if="todo.isDone" class="element element-done">{{ todo.title }}</div>
  </div>

</template>

<script>
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
      this.text.split(/\r?\n/)
          .filter(s => s.length > 0)
          .map(s => this.todos.push({isDone: false, title: s}))
      localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    reset() {
      this.text = ''
      localStorage.setItem('todos', '')
      this.todos = []
    }
  },
  mounted() {
    const storedText = localStorage.getItem('todos');
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
