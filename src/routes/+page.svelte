<script>
	let todos = [
		{ id: 1, name: 'Walk The Dog', status: 'Incomplete', category: 'Personal', date: '2024-09-30', completed: false },
		{ id: 2, name: 'Do Homework', status: 'Incomplete', category: 'School', date: '2024-10-01', completed: false },
	];
	
	let newTodo = '';
	let newCategory = '';
	let newName = '';
	let newDate = '';
	let editingId = null;
	let editingText = '';



	// Add a new todo
	function addTodo() {
		if (newTodo.trim() !== '' && newCategory.trim() !== '' && newDate.trim() !== '') {
			todos = [...todos, { 
				id: Date.now(), 
				name: newTodo, 
				status: 'Incomplete', 
				category: newCategory, 
				date: newDate, 
				completed: false 
			}];
			newTodo = '';
			newCategory = '';
			newDate = '';
		}
	}

	// Handle keypress (enter) for adding todo
	function handleKeyPress(event) {
		if (event.key === 'Enter') {
			addTodo();
		}
	}

	// Delete a todo
	function deleteTodo(id) {
		todos = todos.filter(todo => todo.id !== id);
	}

	// Start editing a todo
	function startEditing(id, name) {
		editingId = id;
		editingText = name;
	}

	// Save edited todo
	function saveEdit(id) {
		if (editingText.trim() !== '') {
			todos = todos.map(todo =>
				todo.id === id ? { ...todo, name: editingText } : todo
			);
		}
		cancelEdit();
	}

	// Cancel editing
	function cancelEdit() {
		editingId = null;
		editingText = '';
	}

	// Handle keypress for editing (Enter to save, Escape to cancel)
	function handleEditKeyPress(event, id) {
		if (event.key === 'Enter') {
			saveEdit(id);
		} else if (event.key === 'Escape') {
			cancelEdit();
		}
	}

	// Toggle complete/incomplete todo
	function toggleComplete(id) {
		todos = todos.map(todo =>
			todo.id === id ? { ...todo, completed: !todo.completed, status: todo.completed ? 'Incomplete' : 'Complete' } : todo
		);
	}

	// Clear all completed todos
	function clearCompleted() {
		todos = todos.filter(todo => !todo.completed);
	}

	// Calculate remaining todos
	$: remainingTodos = todos.filter(todo => !todo.completed).length;
</script>

<main>
	<h1 class="text-purple-800">Todo App</h1>

	<!-- Add Todo Section -->
	<div>
		<input
			type="text"
			bind:value={newTodo}
			on:keypress={handleKeyPress}
			placeholder="Todo Name"
		/>
		<input
			type="text"
			bind:value={newCategory}
			on:keypress={handleKeyPress}
			placeholder="Category"
		/>
		<input
			type="date"
			bind:value={newDate}
			on:keypress={handleKeyPress}
			placeholder="Due Date"
		/>
		<button on:click={addTodo} class="bg-blue-500 text-white p-2 rounded">Add Todo</button>
	</div>

	<!-- Todo List -->
	<ul>
		{#each todos as { id, name, status, category, date, completed }}
			<li>
				<input type="checkbox" checked={completed} on:change={() => toggleComplete(id)} />

				<!-- Show editable input if editing this todo -->
				{#if editingId === id}
					<input
						type="text"
						bind:value={editingText}
						on:blur={() => saveEdit(id)}
						on:keypress={(e) => handleEditKeyPress(e, id)}
						autofocus
						class="border rounded p-2"
					/>
					<button on:click={() => saveEdit(id)} class="bg-green-500 text-white p-2 rounded">Save</button>
					<button on:click={cancelEdit} class="bg-red-500 text-white p-2 rounded">Cancel</button>
				{:else}
					<!-- Normal todo text, editable on double-click -->
					<span
						on:dblclick={() => startEditing(id, name)}
						style="text-decoration: {completed ? 'line-through' : 'none'}"
					>
						{name} - <strong>{category}</strong> - <em>{date}</em> - Status: {status}
					</span>
				{/if}

				<button on:click={() => deleteTodo(id)} class="bg-red-500 text-white p-2 rounded">Delete</button>
			</li>
		{/each}
	</ul>

	<!-- Footer Section -->
	<footer>
		<p>{remainingTodos} todos left</p>
		<button on:click={clearCompleted} class="bg-blue-500 text-white p-2 rounded">Clear completed todos</button>
	</footer>
</main>

<style>
	main {
		font-family: Arial, sans-serif;
		max-width: 600px;
		margin: auto;
	}

	input[type="text"], input[type="date"] {
		padding: 8px;
		margin-right: 8px;
	}

	button {
		padding: 8px;
		margin: 8px;
		cursor: pointer;
	}

	ul {
		list-style-type: none;
		padding: 0;
	}

	li {
		display: flex;
		align-items: center;
		margin-bottom: 8px;
	}

	span {
		margin-left: 8px;
		cursor: pointer;
	}

	input[type="text"], input[type="date"] {
		flex-grow: 1;
	}
</style>

  
  
