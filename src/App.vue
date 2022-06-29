<script setup>
// ref handles state
// onMounted local storage
// computed handles the list and orders it order
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref("")
const content = ref("")
const category = ref (null) 


// order list in ascending order latest on top and oldest under
const todos_ascending = computed(() => todos.value.sort((a, b) =>{
  return b.createdAt - a.createdAt
}))

// if nothing is entered then just return, nothing is added
const addTodo = () => {
  if(content.value.trim() === "" || category === null) {
    return
  }
  
  // add todos to array
  todos.value.push({
    content: content.value,
    category: category.value,
    done: false,
    createdAt: new Date.getTime()
  });
  content.value = "";
  category.value= null;
}
// it will remove which ever item is selected it and set the remaining items in the array  to the todo
const  removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
}


// deep looks through array above and if anything changes will call this function again
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}), {deep: true}

// this will watch name and if it changes set its value to the new value
watch(name, (newVal) => {
  localStorage.setItem("name", newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name')|| ""
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main clas="app">
    <section class="greeting">
       <h2 class="title">
         Good Morning,
         <!-- placeholder for name -->
         <input type="text" placeholder="Name here"
         v-model="name" />

       </h2>
    </section>

    <section class="create-todo">
      <h3> Let's get these done!  </h3>
      <form @submit.prevent="addTodo">
        <h4>Game plan?</h4>
        <input 
          type="text" 
          placeholder="type task here" 
          v-model="content" />

        <h4>Choose a category</h4>

        <div class="options">
          <label>
            <input 
              type="radio"
              name="categories"
              value="school"
              v-model="category" />

              <span class="bubble school"></span>
              <div>School</div>
      </label>
       <label>
        <input 
        type="radio" 
        name="categories"  
        value="home" 
        v-model="category" />

        <span class="bubble home"></span>
        <div>Home</div>
      </label>
      </div>
        <input 
          type="submit" 
          value="Add To List" />

      </form>

    </section>
    <section class="todo-list">
        <h3>THE PLAN</h3>
        <div class="list">
            <div v-for="todo in todos_ascending" 
            :class="`todo-item ${todo-done && 'done'}`">
            <label>
                  <input type="checkbox" vmodel="todo.done" />
                  <span :class="`bubble ${
                    todo.categories ==  'school' ? 'school' : 'home'}`"></span>
             </label>

             <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
           <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
          </div>
    </div>
  </section>
</main>
</template>

     