<template>
	<div>
		<h1>TODO APP</h1>
		<p v-if="error">Por favor escribe algo...</p>
		<form @submit.prevent="procesarFormulario">
			<input
				type="text"
				placeholder="Ingrese todo"
				v-model.trim="textoTodo"
			/>
			<button type="submit">Agregar</button>
		</form>
		<div v-if="arrayTodos.length === 0">¡Empieza a agregar TODOS!</div>
		<ul>
			<li v-for="(item, index) of arrayTodos" :key="index">
				<span>{{ item.texto }}</span>
				<span>{{ item }} </span>
				<button @click="eliminarTodo(item.id)">Eliminar</button>
			</li>
		</ul>
	</div>
</template>

<script>
export default {
	data() {
		return {
			textoTodo: "",
			arrayTodos: [],
			error: false,
		};
	},
	methods: {
		async procesarFormulario() {
			if (this.textoTodo === "") {
				this.error = true;
				return console.log("escribe algo por favor");
			}

			this.error = false;

			const objetoTodo = {
				texto: this.textoTodo,
				id: Date.now(),
			};

			try {
				const res = await fetch(
					`https://app-todo-vue-79e5f-default-rtdb.firebaseio.com/todos/${objetoTodo.id}.json`,
					{
						method: "put",
						headers: {
							"Content-Type": "application/json",
						},
						body: JSON.stringify(objetoTodo),
					}
				);

				if (!res.ok) return console.log("falló al agregar todo");

				this.arrayTodos.push(objetoTodo);
				this.textoTodo = "";
			} catch (error) {
				console.log(error);
			}
		},
		async eliminarTodo(id) {
			try {
				const res = await fetch(
					`https://app-todo-vue-79e5f-default-rtdb.firebaseio.com/todos/${id}.json`,
					{
						method: "delete",
					}
				);

				if (!res.ok) return console.log("Falló al intentar eliminar");

				this.arrayTodos = this.arrayTodos.filter(
					(item) => item.id !== id
				);
			} catch (error) {
				console.log(error);
			}
		},
	},
	async created() {
		try {
			const res = await fetch(
				"https://app-todo-vue-79e5f-default-rtdb.firebaseio.com/todos.json"
			);
			const data = await res.json();

			for (const key in data) {
				this.arrayTodos.push(data[key]);
			}
		} catch (error) {
			console.log(error);
		}
	},
};
</script>
