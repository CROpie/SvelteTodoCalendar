<script>
    import NewTodo from '$root/components/NewTodo.svelte'
    import EditTodo from '$root/components/EditTodo.svelte'

    import { afterUpdate } from 'svelte';
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher()

    export let filteredTodoList;
    export let projectListData;
    export let username;

    let selectedTodo;
    let newTodoFlag = false;
    let editTodoFlag = -1;

    /* PRETTIFYING THE DATE */
    function sortTodosByDate() {
        filteredTodoList = filteredTodoList.sort((a, b) =>
            a.duedate > b.duedate ? 1 : -1
        );
    }
    function flagDateColourCoding() {
        const dateToday = todayPlusInterval(0);
        const dateTomorrow = todayPlusInterval(1);
        const dateDayAfterTomorrow = todayPlusInterval(2);
        filteredTodoList.forEach((todo) => {
            // Flag certain todo dates for text and colour alterations
            if (todo.duedate == dateToday) {
                todo.dateFlag = 'Today';
            } else if (todo.duedate == dateTomorrow) {
                todo.dateFlag = 'Tomorrow';
            } else if (todo.duedate == dateDayAfterTomorrow) {
                todo.dateFlag = 'Day_After_Tomorrow';
            } else if (todo.duedate < dateToday) {
                todo.dateFlag = 'Past';
            }
        });
    }
    function todayPlusInterval(interval) {
        const today = new Date();
        let day = today.getDate();
        let month = today.getMonth() + 1;
        let year = today.getFullYear();

        const int = interval - 1;
        const thirtyDayMonths = [4, 6, 9, 11];

        if (month == '02' && day >= 28 - int) {
            day = day + interval - 28;
            month = month + 1;
        } else if (thirtyDayMonths.includes(month) && day >= 30 - int) {
            day = day + interval - 30;
            month = month + 1;
        } else if (day >= 31 - int && month == '12') {
            day = day + interval - 31;
            month = 1;
            year = year + 1;
        } else if (day >= 30 - int) {
            day = day + interval - 31;
            month = month + 1;
        } else {
            day = day + interval;
        }

        const padMonth = month.toString().padStart(2, 0);
        const padDay = day.toString().padStart(2, 0);
        const newDate = `${year}-${padMonth}-${padDay}`;

        return newDate;
    }
    function alterDateToBePretty() {
        filteredTodoList.forEach((todo) => {todo.prettyduedate = prettifyDate(todo.duedate)});
    }
    function prettifyDate(dateString) {
        const splitString = dateString.split('-');
        const year = splitString[0].slice(2, 4);
        // prettier-ignore
        const monthCode = [
            'Dec', 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec', ];
        let month = splitString[1];
        month = monthCode[parseInt(month)];
        const day = splitString[2];
        const newString = `${day} ${month}\u00A0\u00A0'${year}`;
        return newString;
    }

    /* BOUND EVENTS */
    const clickNewTodo = () => {
        newTodoFlag = true;
        // close an open todo if open
        selectedTodo = -1;
        // close edit if open
        editTodoFlag = -1;
    }
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
        // close edit if open
        editTodoFlag = -1;
    }
    const deleteTodo = (todoID) => {
        dispatch('deleteTodo', todoID)
    }
    const editTodo = (todoID) => {
        editTodoFlag = todoID;
    }

    /* INITIALIZATION */
    // runs after the DOM is synced with the data
    // since getting from DB is async, would otherwise run before the data had arrived (if called immediately).
    afterUpdate(() => {
		sortTodosByDate()
        flagDateColourCoding();
        alterDateToBePretty();
	})

</script>

<div id="todo-container">
    
{#if !newTodoFlag}
    <div class="todo new-todo" on:click={clickNewTodo}>New Todo</div>
{:else}
    <NewTodo { username } { projectListData } on:submitTodo  bind:newTodoFlag/>
{/if}

{#each filteredTodoList as todo (todo.id)}

{#if editTodoFlag === todo.id}
    <EditTodo { todo } on:editTodo bind:editTodoFlag />
{:else}

    <div class="todo" on:click={() => clickTodo(todo.id)}>
        <div class="todo-name">{todo.name}</div>
    {#if todo.dateFlag && todo.dateFlag != 'Past'} <!-- write today, tomorrow, day after tomorrow -->
        <div class="todo-date {todo.dateFlag}">{todo.dateFlag}</div>
    {:else} <!-- past, future write the date itself -->
        <div class="todo-date {todo.dateFlag}">{todo.prettyduedate}</div>
    {/if}
        <div class="todo-del-button" on:click={() => deleteTodo(todo.id)}>✘</div>
    </div>

    {#if selectedTodo === todo.id}
    <div class="open-todo">
        <div class="open-todo-data">
            <div class="todo-desc">{todo.desc}</div>
            <div class="todo-notes">{todo.notes}</div>
        </div>
        <div class="todo-edit-button" on:click={() => editTodo(todo.id)}>E</div>
    </div>
    {/if}

    
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

.todo-name {
    flex: 1;
    pointer-events: none;
}

.todo-date {
    color: lightgreen;
    font-size: 1rem;
    pointer-events: none;
    margin-right: 3rem;

}

.Past {
    color: grey;
}
.Today {
    color: red;
}
.Tomorrow {
    color: orange;
}
.Day_After_Tomorrow {
    color: yellow;
}

.todo-del-button {
    cursor: crosshair;
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

.todo-desc {
    color: rgb(255, 221, 0);
}

.todo-edit-button {
    padding: 0.5rem;
    font-size: 1.5rem;
    background-color: transparent;
}

.todo-edit-button:hover {
    cursor: pointer;
    color: red;
}
</style>