<template>
  <div>Instant to do list!</div>
  <label>
    <textarea
        @paste="onPaste"
        v-model="text"
        @click="text = ''; todos = []"
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
    onPaste(evt) {
      evt.clipboardData.getData('text')
          .split(/\r?\n/)
          .map(s => this.todos.push({isDone: false, title: s}))
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
