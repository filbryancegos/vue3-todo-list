<script>
export default {
 props: ['todo', 'title', 'markCompleted', 'handleUpdate', 'handleDetete'],
}

</script>
<template>
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
</template>


<style scoped>
.iscompleted {
  text-decoration: line-through;
}
</style>
