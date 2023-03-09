<script>
  import { ref, reactive, onMounted, computed } from 'vue'

  export default {
    props: {
      msg: String
    },

    setup(props) {
      const message = ref(props.msg)
      const title = ref('')
      const update = ref(false)
      const currentTodo = ref({});

      const defaultTodo = [{
        id: Math.floor(Math.random() * 100),
          title: 'The quick brown fox jumped over the lazy dog 1',
          completed: false,
      }]

      onMounted(() => {
        const todosLists = JSON.parse(localStorage.getItem("todos")) || defaultTodo;
        todoList.value = todosLists;
        saveTodo()
      })

      const todoList = ref([])

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
        message,
        todoList,
        handleClear,
        title,
        handleDetete,
        onSubmit,
        handleUpdate,
        markCompleted,
        pendingTodo
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
            <div class="grid grid-cols-3 h-10">
              <div class="col-span-2 bg-gray-200 flex items-center py-2 px-4 text-black gap-2">
                <div class="flex items-center gap-2">
                  <input 
                  type="checkbox" 
                  :checked="todo.completed"
                  @input="markCompleted(todo.id)">
                  <h1 :class="{'iscompleted': todo.completed}">{{ todo.title }}</h1>
                </div>
                
              </div>
              <div class="w-full flex">
                
                <button @click="handleUpdate(todo)" :disabled="todo.completed" :class="[todo.completed ? 'bg-gray-300' : 'bg-green-500']" class=" p-2 text-white w-full">edit</button>
                <button @click="handleDetete(todo.id)" class="bg-red-500 p-2 text-white w-full">delete</button>
              </div>
            </div>
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
.read-the-docs {
  color: #888;
}

.iscompleted {
  text-decoration: line-through;
}
</style>
