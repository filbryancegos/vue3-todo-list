<script>
  import { ref, reactive, onMounted, computed } from 'vue'
  import TodoItem  from './TodoItem.vue'

  export default {
    components: {
      TodoItem
    },

    setup() {

      const form = reactive({
        title: ''
      })

      const errors = reactive({
        title: ''
      })

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
        console.log(form.title);
        if (!form.title && form.title === '') {
          errors.title = "pleas input todo list"
          return
        } 

        if (todoList.value.some(todo => todo.title === form.title)) {
          errors.title = "todo list is already exist"
          return
        }

        if (update.value) {
          const todos = todoList.value.map(todo => {
          if (todo.id === currentTodo.value.id) {
            return {...todo, title: form.title}
          }
          return todo
        })

        todoList.value = todos;
        update.value = false;
        currentId.value = null;

        } else {
            todoList.value = [...todoList.value, {
            id: Math.floor(Math.random() * 100),
            title: form.title,
            completed: false,
          }]
        }
        saveTodo()
      }

      const handleUpdate = (payload) => {
        form.title = payload.title;
        update.value = true;
        currentTodo.value = payload;
      }

      const saveTodo = () => {
        form.title = ''
        errors.title = ''
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

      const completedTodo = computed(() => {
        return todoList.value.filter(todo => todo.completed).length;
      })

      const handlePendingTodo = () => {
        todoList.value = todoList.value.filter(todo => todo.completed);
        saveTodo()
      }

      const handleCompletedTodo = () => {
        todoList.value = todoList.value.filter(todo => !todo.completed);
        saveTodo()
      }

      return {
        form,
        todoList,
        handleClear,
        handleDetete,
        onSubmit,
        handleUpdate,
        markCompleted,
        pendingTodo,
        errors,
        completedTodo,
        handlePendingTodo,
        handleCompletedTodo
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
            <div class="grid grid-cols-1 lg:grid-cols-3">
              <div class="col-span-1 lg:col-span-2">
                <input 
                v-model="form.title"
                 type="text" :class="[ errors.title ? 'border-red-500' : '']" class="block w-full border h-10 px-4 py-4" placeholder="">
              </div>
              <button :disabled="!form.title" :class="[!form.title ? 'opacity-50' : 'opacity-100']" type="submit" class="bg-slate-500 text-white flex items-center justify-center w-full">Add Todo</button>

              <div class="col-span-1 lg:col-span-3">
                <span :class="[ errors.title ? 'text-red-600' : '']">{{ errors.title ? errors.title : '' }}</span>
              </div>
            </div>
          </form>
       
          <div class="grid grid-cols-1 lg:grid-cols-3 mt-2" v-for="todo in todoList">
            <TodoItem 
              :todo="todo"
              :handleDetete="handleDetete"
              :handleUpdate="handleUpdate"
              :markCompleted="markCompleted"
            />
          </div>

          <div class="mt-2 bg-slate-200 py-2 px-4 flex justify-between">
            <span>you have {{pendingTodo}} pending task</span>
            <span @click="handlePendingTodo" class="cursor-pointer">clear all pending task</span>
          </div>
          <div class="mt-2 bg-slate-200 py-2 px-4 flex justify-between">
            <span>you have {{completedTodo}} completed tasks</span>
            <span @click="handleCompletedTodo" class="cursor-pointer">clear all completed task</span>
          </div>
          <div class="mt-2 bg-slate-200 py-2 px-4 flex justify-between">
            <span>you have {{todoList.length}} tasks</span>
            <span @click="handleClear" class="cursor-pointer">clear all  task</span>
          </div>
        </div>
      </div>
    </div>
</template>
<style scoped>
</style>
