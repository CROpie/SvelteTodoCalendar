<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher()

    export let todo
    export let editTodoFlag

    // make a new todo without dateFlag & prettyDuedate
    let editedTodo = {
        username: todo.username,
        projectID: todo.projectID,
        name: todo.name,
        desc: todo.desc,
        duedate: todo.duedate,
        notes: todo.notes,
        id: todo.id
    }

    const submitHandler = () => {
        // sent to App.svelte for PUT method
        dispatch('editTodo', editedTodo)
        editTodoFlag = -1
    }

</script>

<form on:submit|preventDefault={submitHandler}>
    <div class="todo new-todo">
        <input class="todo-input-field name" placeholder="New Todo" bind:value={editedTodo.name} required>
        <div class="todo-date"></div>
        <input type="date" class="todo-input-field date" bind:value={editedTodo.duedate} required>
    </div>

    <!-- Changed Notes to be contenteditable div to ensure that the height auto-adjusts as required (doesn't with input)-->

    <div class="open-todo new">
        <div class="open-todo-data">
            <input class="todo-input-field description" placeholder="Description" bind:value={editedTodo.desc}>
            <div class="todo-input-field notes" placeholder="Notes" bind:textContent={editedTodo.notes} contenteditable="true"></div>
        </div>
        <button class="todo-submit-button">✔</button>
    </div>

</form>


<style>

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
        text-shadow: 2px 2px 2px black;
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
    
    .todo-date {
        color: white;
        font-size: 1rem;
        pointer-events: none;
        margin-right: 3rem;
    }
    
    /* new todo */
    
    .todo-input-field {
        background-color: transparent;
        font-size: 1rem;
        color: white;

        box-shadow: 0 0 10px #9ecaed;
        border-radius: 7px;
        padding-left: 1rem;
        margin-left: -1rem;
        outline: none;
        border: none;
        height: auto;
        text-shadow: 2px 2px 2px black;
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
    
        text-shadow: 2px 2px 2px black;
    
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
    
    .open-todo.new {
        margin-top: 0;
    }
    </style>