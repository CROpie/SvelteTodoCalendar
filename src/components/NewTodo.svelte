<script>
    import { createEventDispatcher } from "svelte";

    export let projectListData;
    export let newTodoFlag;
    export let username;

    const dispatch = createEventDispatcher()

    let dropdownProjectID;
    let newTodo = {
        username: username,
        name: '',
        duedate: '',
        desc: '',
        notes: '',
        projectID: '',
    }

    // effect of submitting a todo
    const submitHandler = () => {
        // for some reason, dropdown binding works differently - doesn't apply automatically
        newTodo.projectID = dropdownProjectID;

        // check the data to make sure all necessary fields are filled in

        // submit to database
        dispatch('submitTodo', newTodo)

        // removes <NewTodo> from the DOM, which also has the effect of resetting the newTodo fields via destroying the current instance
        newTodoFlag = false;
    }

</script>

<form on:submit|preventDefault={submitHandler}>
    <div class="todo new-todo">
        <input class="todo-input-field name" placeholder="New Todo" bind:value={newTodo.name} required>
        <div class="todo-date"></div>
        <input type="date" class="todo-input-field date" bind:value={newTodo.duedate} required>
    </div>

    <div class="open-todo new">
        <div class="open-todo-data">
            <input class="todo-input-field description" placeholder="Description" bind:value={newTodo.desc}>

            <!-- Changed Notes to be contenteditable div to ensure that the height auto-adjusts as required (doesn't with input)-->
            <!-- <input class="todo-input-field notes" placeholder="Notes" bind:value={newTodo.notes}> -->
            <div class="todo-input-field notes" data-placeholder="Notes" bind:textContent={newTodo.notes} contenteditable="true"></div>

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
    
    /* shadow present until user has typed in the field*/
    .todo-input-field {
        box-shadow: none;
        outline: none;
        border: none;
    }

    .todo-input-field:placeholder-shown,
    .notes,
    .date:invalid {
        box-shadow: 0 0 10px #9ecaed;
        border-radius: 7px;
        padding-left: 1rem;
        margin-left: -1rem;
    }

    /* need to use this for the contentediable divs because they can't have a true placeholder */
    .notes:empty:before {
        content: attr(data-placeholder);
        color: white;
        opacity: 0.5;
    }
    .notes:not(:empty) {
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
        text-shadow: 2px 2px 2px black;
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
    
    .open-todo.new {
        margin-top: 0;
    }


    </style>