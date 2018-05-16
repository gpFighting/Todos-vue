<template>
  <div id="app">
    <section class="todoapp">
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" placeholder="What needs to be done?" autofocus v-model="input" @keyup.enter="addToTodos">
      </header>
      <template v-if="todos.length">
        <!-- This section should be hidden by default and shown when there are todos -->
        <section class="main">
          <input id="toggle-all" class="toggle-all" type="checkbox" v-model="toggleAll">
          <label for="toggle-all">Mark all as complete</label>
          <ul class="todo-list">
            <!-- These are here just to show the structure of the list items -->
            <!-- List items should get the class `editing` when editing and `completed` when marked as completed -->
            <li :class="{completed: item.completed, editing: dblindex === i}" v-for="(item, i) in changeTodos" :key="i" >
              <div class="view">
                <input class="toggle" type="checkbox" v-model="item.completed">
                <label @dblclick.self.stop="dblindex=i">{{item.todo}}</label>
                <button class="destroy" @click.stop="remove(i)"></button>
              </div>
              <input class="edit" :value="item.todo" @keyup.enter="editTodo(item,$event)" @keyup.esc="dblindex=null" @blur="editTodo(item,$event)">
            </li>
          </ul>
        </section>
        <!-- This footer should hidden by default and shown when there are todos -->
        <footer class="footer">
          <!-- This should be `0 items left` by default -->
          <span class="todo-count"><strong>{{leftTodos}}</strong> item left</span>
          <!-- Remove this if you don't implement routing -->
          <ul class="filters" @click="getStatu($event)">
            <li>
              <a  href="#/" :class="{selected: changed==='all'}">All</a>
            </li>
            <li>
              <a href="#/active" :class="{selected: changed==='active'}">Active</a>
            </li>
            <li>
              <a href="#/completed" :class="{selected: changed==='completed'}">Completed</a>
            </li>
          </ul>
          <!-- Hidden if no completed items are left ↓ -->
          <button class="clear-completed" @click="clearCompleted">Clear completed</button>
        </footer>
      </template>
    </section>
    <footer class="info">
      <p>Double-click to edit a todo</p>
      <!-- Remove the below line ↓ -->
      <p>Template by <a href="http://sindresorhus.com">Sindre Sorhus</a></p>
      <!-- Change this out with your name and url ↓ -->
      <p>Created by <a href="http://todomvc.com">you</a></p>
      <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
    </footer>
  </div>
</template>

<script>
import 'todomvc-common/base.css'
import 'todomvc-app-css/index.css'
export default {
  name: 'App',
  data(){
    return {
      todos: [
        {todo: '学习', completed: true},
        {todo: '睡觉', completed: false},
        {todo: '吃饭', completed: true}
      ],
      input: '',
      dblindex: null,
      changed: 'all'
    }
  },
  created(){
    this.changed = location.hash.split('/')[1] || 'all'
    this.todos = JSON.parse(localStorage.getItem('todos')||'[]')
  },
  beforeUpdate(){
    localStorage.setItem('todos',JSON.stringify(this.todos))
  },
  methods: {
    addToTodos(){
      if (!this.input) {
        return
      }
      this.todos.push({todo: this.input, completed: false})
      this.input = ''
    },
    remove(i){
      this.todos.splice(i, 1)
    },
    clearCompleted(){
      this.todos = this.todos.filter(item=>!item.completed)
    },
    editTodo(item,e){
      if (!e.target.value) {
        return this.dblindex=null
      }
      item.todo = e.target.value
      this.dblindex = null
    },
    getStatu(e){
      this.changed = e.target.text.toLowerCase()
    }
  },
  computed: {
    toggleAll:{
      get(){
        return this.todos.every(item=>item.completed)
      },
      set(newval){
        this.todos.forEach(item=>item.completed=newval)
      }
    },
    leftTodos(){
      return this.todos.filter(item=>!item.completed).length
    },
    changeTodos(){
      switch(this.changed) {
        case 'active': 
        return this.todos.filter(item=>!item.completed);
        break;
        case 'completed':
        return this.todos.filter(item=>item.completed);
        break;
        default:
        return this.todos;
        break;
      }
    }
  }
}
</script>

<style>

</style>
