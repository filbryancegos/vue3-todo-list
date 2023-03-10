<script>
export default {
 props: ['todo', 'markCompleted', 'handleUpdate', 'handleDetete'],
}

</script>
<template>
    <div class="col-span-1 lg:col-span-2 bg-gray-200 flex items-center py-2 px-4 text-black gap-2">
      <div class="flex items-center gap-2">
        <input 
        type="checkbox" 
        :checked="todo.completed"
        @input="markCompleted(todo.id)">
        <h1 :class="{'iscompleted': todo.completed}">{{ todo.title }}</h1>
      </div>
    </div>
    <div class="w-full flex">
      <div @click="handleUpdate(todo)" :disabled="todo.completed" :class="[todo.completed ? 'opacity-50' : 'opacity-100']" class=" bg-green-500 p-2 text-white w-full flex items-center justify-center">edit</div>
      <div @click="handleDetete(todo.id)" :disabled="todo.completed" :class="[todo.completed ? 'opacity-50' : 'opacity-100']" class="bg-red-500 p-2 text-white w-full flex items-center justify-center">delete</div>
    </div>
  
</template>


<style scoped>
.iscompleted {
  text-decoration: line-through;
}
</style>
