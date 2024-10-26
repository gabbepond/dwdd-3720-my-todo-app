<script>
    let todos = [
        { id: 1, name: 'Walk The Dog', status: 'Incomplete', category: 'Personal', date: '2024-09-30', completed: false },
        { id: 2, name: 'Do Homework', status: 'Incomplete', category: 'School', date: '2024-10-01', completed: false },
    ];

    let categories = ['Personal', 'Work', 'School', 'Fitness']; // Predefined categories
    let newTodo = '';
    let newCategory = '';
    let newDate = '';
    let customCategory = ''; // To hold the custom category input
    let addingNewCategory = false; // Tracks whether the user is adding a new category
    let showDropdown = false; // Toggle state for dropdown visibility
	let editingId = null;
	let editedTodo = { name: '', date: '' };
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
            showDropdown = false; // Hide dropdown after adding
        }
    }


    // Add a new custom category
    function addCategory() {
        if (customCategory.trim() !== '' && !categories.includes(customCategory)) {
            categories = [...categories, customCategory];
            newCategory = customCategory; // Set the new category as the selected one
            customCategory = ''; // Clear input field after adding
            addingNewCategory = false; // Hide the input field
            showDropdown = false; // Hide dropdown after adding
        }
    }

    // Delete a category if it's not in use
    function deleteCategory(categoryToDelete) {
        const inUse = todos.some(todo => todo.category === categoryToDelete);
        if (!inUse) {
            categories = categories.filter(category => category !== categoryToDelete);
        } else {
            alert(`Category "${categoryToDelete}" is in use and cannot be deleted.`);
        }
    }

    // Handle category selection
    function handleCategoryChange(category) {
        newCategory = category;
        addingNewCategory = false; // Hide input if an existing category is selected
        showDropdown = false; // Hide the dropdown after selecting a category
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

// function saveTodo() {
//     todos = todos.map(todo =>
//         todo.id === editingId ? { ...todo, name: editedTodo.name, date: editedTodo.date } : todo
//     );
// }

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
			>
       
        <div class="dropdown">
            <button class="dropdown-button" on:click={() => showDropdown = !showDropdown}>
                {newCategory || 'Select Category'}
            </button>
            {#if showDropdown}
                <ul class="dropdown-menu">
                    {#each categories as category}
                        <li class="categorylist">
                            <button on:click={() => deleteCategory(category)} class="bg-red-500 text-white ml-4 p-1 rounded">x </button>
                            <span on:click={() => handleCategoryChange(category)}>{category}</span>
                        </li>
                    {/each}
                    <li>
                        <!-- Show input for new category instead of a button -->
                        {#if addingNewCategory}
                            <input
                                type="text"
                                bind:value={customCategory}
                                placeholder="New Category"
                                on:keypress={(e) => { if (e.key === 'Enter') addCategory(); }}
                            />
                            <button on:click={addCategory} class="bg-green-500 text-white p-2 rounded">Add</button>
                        {:else}
                            <button on:click={() => addingNewCategory = true}>+ Add New Category</button>
                        {/if}
                    </li>
                </ul>
            {/if}
        </div>
		

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

    input[type="text"], input[type="date"], select {
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

    input[type="text"], input[type="date"], select {
        flex-grow: 1;
    }

    .dropdown {
        position: relative;
        display: inline-block;
    }

    .dropdown-menu {
        background-color: aqua;
        color: black;
        position: absolute;
        padding-left: 2px;
        padding-right: 2px;
        z-index: 1;
        list-style-type: none;
    }

    .dropdown-button {
        padding: 8px;
        border: 1px solid #ccc;
        cursor: pointer;
    }

    .dropdown ul {
        list-style-type: none;
        padding: 0;
        margin: 0;
    }

    .mt-4 {
        margin-top: 1rem;
    }
</style>

  
  
  
