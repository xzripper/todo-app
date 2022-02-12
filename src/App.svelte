<!-- Simple TODO site in Svelte. -->

<script>
	// Animations.
	import { slide, scale } from "svelte/transition";

	// User todo's.
	$: Todos = []

	// Todo data.
	let Title, Text

	// Version.
	const Version = 1.0

	// Current year.
	const Year = new Date().getFullYear()

	// Page title.
	const PageTitle = "TODO App."

	// Create new todo.
	function NewTodo() {
		if(Title === undefined) { alert("Title must be filled."); return }
		if(Text === undefined) { alert("Text must be filled."); return }
		if(Title.length > 60) { alert("The title must be less than 60 characters."); return }
		if(Text.length > 600) { alert("The text must be less than 600 characters."); return }
		if(Title.length <= 1) { alert("The title must be more than 1 character."); return }
		if(Text.length <= 1) { alert("The text must be more than 1 character."); return }

		for(let Todo of Todos) {
			if(Todo.TodoTitle === Title) {
				alert("This title is alredy exist.")

				return
			}
		}

		let CurrentDate = new Date()

		let Day = CurrentDate.getDate()
		let Month = CurrentDate.getMonth() + 1
		let Year = CurrentDate.getFullYear()
		let Hours = CurrentDate.getHours()
		let Minutes = CurrentDate.getMinutes()

		if(Month <= 9) {
			Month = `0${Month}`
		}

		if(Minutes <= 9) {
			Minutes = `0${Minutes}`
		}

		let DateToday = `${Day}.${Month}.${Year} ${Hours}:${Minutes}.`

		Todos = [...Todos]
		Todos.push({TodoTitle: Title, TodoText: Text, TodoHaveDone: false, TodoCreated: DateToday})
	}

	// Remove todo.
	function RemoveTodo(TodoPosition) {
		if(window.confirm("Are you sure?")) {
			if(TodoPosition === 0) {
				Todos = [...Todos]
				Todos.shift()
			} else {
				Todos = [...Todos]
				Todos.splice(TodoPosition, TodoPosition)
			}
		}
	}

	// Clear all todo's.
	function ClearTodos() {
		if(window.confirm("Are you sure?")) {
			if(Todos.length === 0) {
				alert("No todo's found.")

				return
			}

			Todos = [...Todos]
			Todos = []
		}
	}

	// Mark todo as done, if alredy done mark as un-done.
	function MarkAsDoneOrNot(TodoPosition) {
		Todos = [...Todos]
		Todos[TodoPosition].TodoHaveDone = !Todos[TodoPosition].TodoHaveDone
	}

	// Save all todo's in cookies.
	function SaveTodos() {
		if(Todos.length > 0) {
			document.cookie = JSON.stringify(Todos)

			alert("Todo's saved.")
		} else {
			alert("Todo's are empty.")
		}
	}

	// Load all todo's from cookies to page.
	function LoadTodos() {
		if(document.cookie !== "") {
			Todos = [...Todos]
			Todos = JSON.parse(document.cookie)

			if(Todos.length === 0) {
				alert("Todo's are empty.")

				return
			}

			alert("Todo's loaded.")
		} else {
			alert("Todo's not saved.")
		}
	}

	// Clear all saved todo's from cookies and page.
	function ClearSavedTodos() {
		if(window.confirm("Are you sure?")) {
			document.cookie = "[]"

			Todos = [...Todos]
			Todos = []

			window.location.reload()
		}
	}

	// Update page title.
	document.title = PageTitle

	// Welcome message.
	console.log("Welcome!")
</script>

<style>
	/* Note Div Css. */
	div.NoteDiv {
		border: 3px solid;
		border-radius: 10px;
		border-color: white;
	}

	/* Centered Div. */
	div.Centered {
		text-align: center;
	}

	/* Underlined Label. */
	p.underlined {
		text-decoration: underline;
	}

	/* Font for p. */
	p.WFont {
		font-family: Courier, monospace;
		font-weight: 600;
	}

	/* Centered + font for span. */
	span.cent-WFont {
		font-family: Courier, monospace;
		font-weight: 600;
		text-align: center;
		color: white;
	}

	/* Font for button. */
	button.WFont {
		font-family: Courier, monospace;
		font-weight: 600;
	}

	/* Centered and font for button. */
	button.cent-WFont {
		text-align: center;
		font-family: Courier, monospace;
		font-weight: 600;
	}

	/* Global stylesheets for tags. */
	:global(body) {
		background-color: rgb(30, 30, 30);
	}

	:global(h2) {
		color: white;
	}

	:global(h3) {
		color: white;
	}

	:global(p) {
		color: white;
	}
</style>

<body>
	<main>
		<!-- Welcome message. -->
		<h2>Simple TODO app.</h2>

		<!-- Create new todo. -->
		<h3>Create new TODO.</h3>

		<input bind:value={Title} id="UserTodoTitle" placeholder="TODO Title." size=50><br>
		<textarea bind:value={Text} id="UserTodoText" placeholder="TODO Text." rows=10 cols=52></textarea><br>

		<button class="WFont" on:click={NewTodo}>New TODO.</button><br>

		<!-- List of todo's. -->
		<h3>Todo's.</h3>
		<p class="WFont"><i>A list of todo's. ({Todos.length}).</i></p>
		<div><p class="underlined" on:click={ClearTodos}><i>Delete all.</i></p></div>

		{#each Todos as Todo, TodoPosition}
			<div transition:slide class="NoteDiv">
				<p class="WFont" align="center"><b><i>Created: {Todo.TodoCreated}</i></b></p>
				<p class="WFont" align="center"><b><i>Title: {Todo.TodoTitle}</i></b></p>
				<p class="WFont" align="center"><b><i>Text:</i></b></p>

				{#if Todo.TodoText.split("\n").length > 1}
					{#each Todo.TodoText.split("\n") as Line}
						<div class="Centered">
							<span class="cent-WFont"><b>{Line}</b></span>
						</div>
					{/each}
				{:else if Todo.TodoText.split("\n").length >= 1}
					<div class="Centered">
						<p class="cent-WFont"><b>{Todo.TodoText}</b></p>
					</div>
				{/if}

				{#if Todo.TodoHaveDone}
					<p transition:scale="{{duration: 1000}}" class="WFont" align="center" style="color: rgb(0, 255, 0);"><b><i>TODO Done.</i></b></p>
				{:else if !Todo.TodoHaveDone}
					<p transition:scale="{{duration: 1000}}" class="WFont" align="center" style="color: rgb(255, 0, 0);"><b><i>TODO Not Done.</i></b></p>
				{/if}

				<div class="Centered"><button class="cent-WFont" on:click={() => RemoveTodo(TodoPosition)}>Delete TODO.</button></div>
				<div class="Centered"><button class="cent-WFont" on:click={() => MarkAsDoneOrNot(TodoPosition)}>Mark as done or un-done.</button></div>
			</div><br>
		{/each}

		<!-- Save or load todo's. -->
		<h3>Todo Save-Load.</h3>

		<button class="WFont" on:click={SaveTodos}>Save todo's.</button>
		<button class="WFont" on:click={LoadTodos}>Load todo's.</button>

		<button class="WFont" on:click={ClearSavedTodos}>Clear saved todo's.</button>

		<!-- Information block. -->
		<h3>Information.</h3>

		<p>
			<i>
				Simple TODO site!<br>
				Site contains default function on TODO app.<br>
				You can create, delete, mark as done, save notes, load notes, clear all saved notes, delete all notes.<br>
				App have simple, small, cosy UI.<br>
				Have good using!<br><br>
				Programming technologies: HTML, CSS, JS, Svelte.<br>
				Author: Ivan Perzhinsky.<br>

				{#if Version === 1.0}
					<span>Version: 1.0.</span><br>
				{:else}
					<span>Version: {Version}.</span><br>
				{/if}

				License: MIT.
			</i>
		</p>

		<p align="center">{Year} ©</p><br>
	</main>
</body>
