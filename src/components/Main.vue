<template>
  <div>Instant to do list!</div>
  <button @click="createTodoList" v-if="todos.length === 0">Create todo list</button>
  <button @click="reset" v-if="todos.length > 0">Reset</button>
  <label v-if="todos.length === 0">
    <textarea
        v-model="text"
    ></textarea>
  </label>
  <br/>
  <div v-for="todo in todos"
       :key="todo.title"
       @click="todo.isDone = !todo.isDone"
       class="todo"
  >
    <div v-if="todo.isDone" class="todoDone">[X] {{ todo.title }}</div>
    <div v-if="!todo.isDone">[] {{ todo.title }}</div>
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
               .map(s => this.todos.push({isDone: false, title: s}))
    },
    reset() {
      this.text = ''
      this.todos = []
    }
  }
}

</script>

<style scoped>
.todo {
  cursor: pointer
}

.todoDone {
  text-decoration: line-through
}
</style>
