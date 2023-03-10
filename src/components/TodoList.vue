<script>
  import { ref, reactive, onMounted, computed } from 'vue'
  import TodoItem  from './TodoItem.vue'

  export default {
    components: {
      TodoItem
    },

    setup() {
      const title = ref('')
      const update = ref(false)
      const currentTodo = ref({});

      const defaultTodo = [{
        id: Math.floor(Math.random() * 100),
          title: 'The quick brown fox jumped over the lazy dog 1',
          completed: false,
      }]

      const todosLists = JSON.parse(localStorage.getItem("todos")) || defaultTodo;
      const todoList = ref(todosLists)

      const handleClear = () => {
        todoList.value = [];
        saveTodo()
      }

      const handleDetete = (id) => {
        todoList.value = todoList.value.filter(todo => todo.id !== id)
        saveTodo()
      }

      const onSubmit = () => {
        if (!title.value && title.value === '') return
        if (update.value) {
          const todos = todoList.value.map(todo => {
          if (todo.id === currentTodo.value.id) {
            return {...todo, title: title.value}
          }
          return todo
        })

        todoList.value = todos;
        update.value = false;
        currentId.value = null;

        } else {
            todoList.value = [...todoList.value, {
            id: Math.floor(Math.random() * 100),
            title,
            completed: false,
          }]
        }
        saveTodo()
      }

      const handleUpdate = (payload) => {
        title.value = payload.title;
        update.value = true;
        currentTodo.value = payload;
      }

      const saveTodo = () => {
        localStorage.setItem("todos", JSON.stringify(todoList.value))
      }

      const markCompleted = (id) => {
        const todos = todoList.value.map(todo => {
          if (todo.id === id) {
            return {...todo, completed: !todo.completed}
          }
          return todo
        })
        todoList.value = todos;
        saveTodo()
      }

      const pendingTodo = computed(() => {
        return todoList.value.filter(todo => !todo.completed).length;
      })

      return {
        todoList,
        handleClear,
        title,
        handleDetete,
        onSubmit,
        handleUpdate,
        markCompleted,
        pendingTodo,
      }
    },
  }
</script>

<template>
    <div class="countainer flex justify-center mt-5 items-center flex-col">
      <div class="h-96 lg:w-1/3">
        <h1 class="text-2xl font-bold">Todo App</h1>
        <div class="mt-4">
          <form @submit.prevent="onSubmit">
            <div class="grid grid-cols-3">
              <div class="col-span-2">
                <input v-model="title" type="text" class="block w-full" placeholder="">
              </div>
              <button type="submit" class="bg-slate-500 text-white flex items-center justify-center w-full">Add Todo</button>
            </div>
          </form>
       
          <div class="mt-3" v-for="todo in todoList">
            <TodoItem 
              :title="'title'"
              :todo="todo"
              :handleDetete="handleDetete"
              :handleUpdate="handleUpdate"
              :markCompleted="markCompleted"
            />
          </div>

          <div class="mt-2 bg-slate-200 py-2 px-4 flex justify-between">
            <span>you have {{pendingTodo}} pending task</span>
            <span @click="handleClear" class="cursor-pointer">clear all</span>
          </div>

        </div>
      </div>
    </div>
</template>
<style scoped>
</style>
