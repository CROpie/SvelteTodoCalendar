<script>
    import { createEventDispatcher } from "svelte";

    export let projectListData

    const dispatch = createEventDispatcher()

    let dropdownProjectID;
    let newTodo = {
        username: 'default',
        name: '',
        duedate: '',
        desc: '',
        notes: '',
        projectID: '',
    }

    /* not sure whether to use preventDefault or not 
        using it means have to reset todo fields, figure out a way to change newTodoFlag in TodoView, 
        and have to either re-get the data from the database or somewhat pointlessly push it onto the TodoListArray

        not using it: faster? Also certain variables such as projectSelection are already in place
    */
    // effect of submitting a todo
    const submitHandler = () => {
        // for some reason, dropdown binding works differently - doesn't apply automatically
        newTodo.projectID = dropdownProjectID;
        console.log(newTodo)
        // check the data to make sure all necessary fields are filled in

        // submit to database
        dispatch('submitTodo', newTodo)
        resetTodoFields()
    }

    const resetTodoFields = () => {
        newTodo.name = '';
        newTodo.duedate = '';
        newTodo.desc = '';
        newTodo.notes = '';
        newTodo.projectID = '';
        // reset projectDropdownID
    }

</script>

<form on:submit|preventDefault={submitHandler}>
    <div class="todo new-todo">
        <input class="todo-input-field name" placeholder="New Todo" bind:value={newTodo.name}>
        <div class="todo-date"></div>
        <input type="date" class="todo-input-field date" bind:value={newTodo.duedate}>
    </div>


    <div class="open-todo new">
        <div class="open-todo-data">
            <input class="todo-input-field description" placeholder="Description" bind:value={newTodo.desc}>
            <input class="todo-input-field notes" placeholder="Notes" bind:value={newTodo.notes}>
            <select id="project-dropdown" bind:value={dropdownProjectID}>
                {#each projectListData as project}
                    <option class="dropdown-option" value={project.id}>{project.projectName}</option>
                {/each}
            </select>
        </div>
        <button class="todo-submit-button">✔</button>
    </div>

</form>

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