<script>
    import { afterUpdate } from 'svelte';
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher()

    const monthList = ['Blank', 'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
    const dayOfWeekList = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']

    export let filteredTodoList;
    export let selectedTodo;

    export let newTodoFlag;
    export let clickedDate;

    let blankTodo = {
        username: "",
        name: "",
        duedate: "",
        desc: "",
        notes: "",
        projectID: -1,
        id: -1,
    }

    /* Calendar Grid */
    const dateInfo = {
        todayDate: undefined,
        dayOfWeek: undefined,
        day: undefined,
        month: undefined,
        year: undefined,
    };
    const chosenDate = {
        day: undefined,
        month: undefined,
        year: undefined,
    }

    let numberOfBlanks;
    let daysInCurrentMonth;

    // setup
    function getDateInfo() {
        const todayDate = new Date();
        dateInfo.todayDate = todayDate;
        dateInfo.dayOfWeek = todayDate.getDay();
        dateInfo.day = todayDate.getDate();
        dateInfo.month = todayDate.getMonth() + 1;
        // dateInfo.month = 1;
        dateInfo.year = todayDate.getFullYear();

        // set the initial date of the calendar
        chosenDate.month = dateInfo.month;
        chosenDate.year = dateInfo.year;
    }
    function calculateBlanks() {
        const initialDay = new Date(`${chosenDate.month} 01 ${chosenDate.year}`);
        const initialDayOfWeek = initialDay.getDay();
        if (initialDayOfWeek === 0) {
            numberOfBlanks = 6;
        } else {
            numberOfBlanks = initialDayOfWeek - 1;
        }
    }
    function calculateDaysInCurrentMonth() {
        const daysInMonthNonLeapArray = ['0', '31', '28', '31', '30', '31', '30', '31', '31', '30', '31', '30', '31']
        daysInCurrentMonth = parseInt(daysInMonthNonLeapArray[chosenDate.month]);
    }
    // change the month
    function clickPrevMonthBtn() {
        chosenDate.month -= 1;
        if (chosenDate.month === 0) {
            chosenDate.month = 12;
            chosenDate.year -= 1;
        }
        calculateBlanks()
        calculateDaysInCurrentMonth()
        filterTodosByMonth()
    }
    function clickNextMonthBtn() {
        chosenDate.month += 1;
        if (chosenDate.month === 13) {
            chosenDate.month = 1;
            chosenDate.year += 1;
        }
        calculateBlanks()
        calculateDaysInCurrentMonth()
        filterTodosByMonth()
    }

    // todos
    let chosenMonthTodoList = []

    function filterTodosByMonth() {
        // due date is "YYYY-MM-DD"
        chosenMonthTodoList = filteredTodoList
            .filter((todo) => todo.duedate.slice(5, 7) == chosenDate.month)
            .filter((todo) => todo.duedate.slice(0, 4) == chosenDate.year);
    }
    
    /* wanted to find a way to print the todos looping through the array once only
    function appendTodos() {
        chosenMonthTodoList.forEach((todo) => {
            const currentDate = todo.duedate.slice(8, 10)
            const currentTile = dateList[currentDate]
            const todoHTML = `<div class='calendar-todo'>${todo.name}</div>`
            currentTile.insertAdjacentHTML('beforeend', todoHTML);
        })
    }*/
    
    getDateInfo()
    calculateBlanks()
    calculateDaysInCurrentMonth()

    // need to use afterUpdate, init and onMount just gave blank arrays
    afterUpdate(() => {
		filterTodosByMonth()
        colourCodeTodos()
	})

    function colourCodeTodos() {
        const projectIDSet = new Set()
        chosenMonthTodoList.forEach((todo) => {
            projectIDSet.add(todo.projectID)
        })
        const colourArray = [...projectIDSet]

        chosenMonthTodoList.forEach((todo) => {
            const index = colourArray.indexOf(todo.projectID)
            todo.colourCode = `projectColour${index}`
        })
    }
    /* bound events */
    function clickBlankArea() {
        selectedTodo = blankTodo;
        newTodoFlag = false;
    }
    function clickNewTodo(day) {
        newTodoFlag = true;
        const chosenDay = day.toString().padStart(2, '0');
        const chosenMonth = chosenDate.month.toString().padStart(2, '0');
        clickedDate = `${chosenDate.year}-${chosenMonth}-${chosenDay}`;
        
    }
    function clickTodo(todo) {
        selectedTodo = todo;
        newTodoFlag = false;
    }

</script>

<div id="calendar-grid-container" on:click|capture={clickBlankArea}>
    
    <div id="month-menu-container">
        <button class="menu-button" on:click={clickPrevMonthBtn}>⬅</button>
        <div id="month-menu-month">{monthList[chosenDate.month]} {chosenDate.year}</div>
        <button class="menu-button" on:click={clickNextMonthBtn}>➡</button>
    </div>

    {#each dayOfWeekList as dayOfWeek}
        <div class="dayOfWeek">{dayOfWeek}</div>
    {/each}
    
    {#each Array(numberOfBlanks) as _, index (index)}
        <div class='date blank'></div>
    {/each}

    {#each Array(daysInCurrentMonth) as _, index (index)}
        <!--<div class='date' bind:this={dateList[(index+1).toString().padStart(2, '0')]}>{index + 1}-->
        
        <div class='date' on:click|capture={() => clickNewTodo(index+1)}>{index + 1}
        {#each chosenMonthTodoList as todo (todo.id)}
            {#if todo.duedate.slice(8, 10) == (index + 1)}
                <div class='calendar-todo {todo.colourCode}' on:click={() => clickTodo(todo)}>{todo.name}</div>
            {/if}
        {/each}
        
        </div>
    {/each}

</div>

<style>
    #calendar-grid-container {
    height: 100vh;

    margin: 0rem 1rem;
    border: 2px solid red;
    background-color: var(--calendar-background);

    display: grid;
    width: 90%;

    grid-template-columns: repeat(7, 14.28%);
    grid-template-rows: 50px 50px repeat(6, 150px);
}

#month-menu-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    grid-column: 1/8;
}

.menu-button {
    background-color: transparent;
    font-size: 3rem;
    color: orange;
    padding: 0rem 2rem;
    text-shadow: 2px 2px 2px black;
}

#month-menu-month {
    color: orange;
    font-size: 3rem;
    text-shadow: 2px 2px 2px black;
}

.menu-button:hover {
    color: blueviolet;
}
.date,
.dayOfWeek {
    color: white;
    border: 1px solid white;
    background-color: none;
    padding: 0.5rem;
}

.dayOfWeek {
    font-size: 2rem;
}

.date {
    font-size: 1rem;
}
.date.blank {
    border: none;
}
.calendar-todo {
    font-size: 0.75rem;
    color: black;

    border-radius: 1rem;
    background-color: lightgreen;
    margin: 0.25rem 0.5rem 0rem 0.5rem;
    padding: 0.1rem 0.5rem;
    cursor: pointer;
}

.calendar-todo:hover {
    background-color: yellow;
}
.projectColour0 {
    background-color: red;
}
.projectColour1 {
    background-color: orange;
}
</style>