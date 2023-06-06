<script>
    import NewTodo from '$root/components/NewTodo.svelte'

    export let filteredTodoList;
    export let projectListData;

    let selectedTodo;
    let newTodoFlag = false;

    // functions to sort the date and make it look nicer
    function sortTodosByDate() {
        filteredTodoList = filteredTodoList.sort((a, b) =>
            a.duedate > b.duedate ? 1 : -1
        );
    }

    // effect of clicking new todo
    const clickNewTodo = () => {
        newTodoFlag = true;
        // close an open todo if open
        selectedTodo = -1;
    }

    // effect of clicking a todo
    const clickTodo = (todoID) => {
        // clicking an open todo will close it
        // otherwise, open it
        if (selectedTodo === todoID) {
            selectedTodo = -1
        } else {
            selectedTodo = todoID;
        }
        // close new todo if open
        newTodoFlag = false;
    }

    function initialize() {
        sortTodosByDate()
        console.log('??')
    }

    sortTodosByDate()

</script>

<div id="todo-container">
    {#if !newTodoFlag}

    <div class="todo new-todo" on:click={clickNewTodo}>New Todo</div>

    {:else}

    <NewTodo { projectListData } on:submitTodo />

    {/if}

    {#each filteredTodoList as todo (todo.id)}

    <div class="todo" on:click={() => clickTodo(todo.id)}>
        <div class="todo-name">{todo.name}</div>
        <div class="todo-date">{todo.duedate}</div>
        <div class="todo-del-button">✘</div>
    </div>

        {#if selectedTodo === todo.id}

    <div class="open-todo">
        <div class="open-todo-data">
            <div class="todo-desc">{todo.desc}</div>
            <div class="todo-notes">{todo.notes}</div>
        </div>
    </div>
        {/if}

    {/each}
</div>

<style>

#todo-container {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    margin-bottom: 1rem;
}
/* todos */
.todo {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 1rem 1rem 5rem;
    border: 2px solid black;
    color: white;
    margin-left: 2rem;
    margin-right: 2rem;


    background-color: rgba(211, 211, 211, 0.2);
    --font-shadow: 2px 2px 2px black;
    font-size: 1rem;
}

.new-todo {
    margin-top: 0.5rem;
    color: greenyellow;
}
.todo:hover {
    background-color: rgba(138, 43, 226, 0.7);
}

.todo:active {
    background-color: rgba(211, 211, 211, 0.2);
}



.todo-name {
    flex: 1;
    pointer-events: none;
}

.todo-date {
    color: white;
    font-size: 1rem;
    pointer-events: none;
    margin-right: 3rem;
}


.todo-del-button {
    padding-right: 0.5rem;
}

.todo-del-button:hover {
    color: red;
}

.todo-del-button {
    visibility: hidden;
}

/* hover todo to show delete button */
.todo:hover .todo-del-button,
.todo-del-button:hover {
    visibility: visible;
}

/* new todo */

.todo-input-field {
    background-color: transparent;
    font-size: 1rem;
    color: white;
}

.todo-input-field.name {
    color: greenyellow;
}
.todo-input-field.date {
    margin-right: 2rem;
}
.todo-input-field.description {
    font-size: 1.5rem;
    color: rgb(255, 221, 0);
}
.todo-input-field.notes {
    font-size: 1.5rem;
}

/* shadow present until user has typed in the field*/
.todo-input-field:placeholder-shown {
    box-shadow: 0 0 10px #9ecaed;
    border-radius: 7px;
    padding-left: 1rem;
    margin-left: -1rem;
}

.todo-input-field {
    box-shadow: none;
    outline: none;
    border: none;
}

/* dropdown-specific */
#project-dropdown {
    border: none;
    background-color: transparent;
    font-size: 1.5rem;
    color: orange;
}

option {
    color: white;
    background-color: black;
}

option:checked {
    color: orange;
}

option:hover {
    background-color: blueviolet;
}

.todo-submit-button {
    padding: 0.5rem;
    font-size: 1.5rem;
    background-color: transparent;
    border: none;
    color: white;
}

.todo-submit-button:hover {
    color: lime;
}

/* open todo */
.open-todo {
    padding: 1rem 1rem 1rem 5rem;
    gap: 3rem;
    border: 2px solid black;
    background-color: rgba(79, 183, 192, 0.7);
    margin-left: 2rem;
    margin-right: 2rem;
    margin-top: -1rem;
    color: white;
    font-size: 1.5rem;

    --font-shadow: 2px 2px 2px black;

    display: flex;
    justify-content: space-between;
    align-items: center;
}

.open-todo-data {
    flex: 1;
    word-wrap: break-word;
    white-space: pre-wrap;

    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.todo-desc {
    color: rgb(255, 221, 0);
}

.open-todo.new {
    margin-top: 0;
}
</style>