<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">What's up, <input type="text" id="name" placeholder="Name here" v-model="name"></h2>
		</section>

		<section class="create-todo">
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h3>Add Your Today Todo's</h3>
				<input type="text" name="content" id="content" placeholder="Make a todo in Vue.js"
					v-model="input_content" />

				<!-- <h3>Pick a category</h3>
				<div class="options">
					<label>
						<input type="radio" name="category" id="category1" value="Important" v-model="input_category" />
						<span class="bubble Important"></span>
						<div>Important</div>
					</label>

					<label>
						<input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>
				</div> -->

				<input type="submit" value="Add todo" />
			</form>
		</section>


		<section class="todo-list" v-if="todos.length > 0">
			<div class="todo-count">
				<p>Total Todos: {{ totalTodos }}</p>
				<p>Completed: {{ completedTodos }}</p>
				<p>Remaining: {{ remainingTodos }}</p>
			</div>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${todo.category == 'Important'
							? 'Important'
							: 'personal'
							}`"></span>
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
  
<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

const addTodo = () => {
	if (input_content.value.trim() === '') {

		alert('Todo field cannot be empty!')
		return
	}

	const now = new Date();
	const formattedDate = `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')}`;
	const formattedTime = `${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`;

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: now.getTime(),
		createdAtFormatted: `${formattedDate} ${formattedTime}`
	})

	input_content.value = ''
	input_category.value = null
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

const totalTodos = computed(() => todos.value.length)
const completedTodos = computed(() => todos.value.filter(todo => todo.done).length)
const remainingTodos = computed(() => totalTodos.value - completedTodos.value)
</script>
