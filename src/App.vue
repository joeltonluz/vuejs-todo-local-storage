<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue';

const nameDefault = 'Fulano de Tal';

type ITodo = {
  content: string;
  category: string;
  done: boolean;
  createdAt: number;
}


const todos = ref<ITodo[]>([])
const name = ref<string>('')

const input_content = ref<string>('')
const input_category = ref<string>('')

const todos_asc = computed(() => todos.value.sort(
  (a, b) => Number(b.createdAt) - Number(a.createdAt)
))

const addTodo = () => {
  if (input_content.value.trim() === '') {
    alert('Escreva a tarefa !')
    return
  }

  if (input_category.value.trim() === '') {
    alert('Escolha uma categoria !')
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = '';
  input_category.value = '';
}

const removeTodo = (todoDeleted: ITodo) => {
  todos.value = todos.value.filter((todo) => todo !== todoDeleted)
}

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal.trim()!=='' ? newVal: nameDefault)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || nameDefault
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Opa e ai,
        <input type="text" placeholder="Digite seu nome aqui" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>GERENCIAMENTO DE TAREFAS</h3>

      <form @submit.prevent="addTodo()">
        <h4>O que está na sua lista de tarefas!?</h4>
        <input type="text" placeholder="ex: criar um vídeo" ref="input_contentx" v-model="input_content" />

        <h4>Escolha uma categoria</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Trabalho</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Pessoal</div>
          </label>
        </div>

        <input type="submit" value="Adicionar Tarefa">
      </form>
    </section>

    <section class="todo-list">
      <h3>Lista de Tarefas</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content"/>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Excluir</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
