<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher()

    export let usernameListData;

    let username = ''

    const submitHandler = () => {
        if (!username) {
            return
        } else {
            username = username.toLowerCase()
            if (username === 'default') {
                // set up default profile
                console.log("Generating default profile.")
                DEFAULTDATA.setUpDefaultData('default')
            } else if (!usernameListData.includes(username)) {
                // create new user
                console.log("Username not recognized. Creating new entry.")
                const newUsername = { username }
                dispatch('addNewUser', newUsername)
            } else {
                // log in existing user
                console.log("Logging in recognized user.")
                dispatch('loginSuccess', username)
            }
        }
    }

    /* DEFAULT DATA */
    const DEFAULTDATA = (() => {
        async function setUpDefaultData(defaultUsername) {

            await findAndDeleteExistingDefaultData(defaultUsername)

            const projectList = makeProjectList(defaultUsername)
            await addDefaultProjectListToDB(projectList)

            const newProjectList = await retrieveDefaultProjectList(defaultUsername)
            const todoList = makeTodoList(defaultUsername, newProjectList)
            await addDefaultTodoListToDB(todoList)

            dispatch('loginSuccess', defaultUsername)
        }
        // Creating the default data
        function makeProjectList(defaultUsername) {
            const projectList = [
                {
                    username: defaultUsername,
                    projectName: 'Lab Duties',
                },
                {
                    username: defaultUsername,
                    projectName: 'Catalysis',
                },
            ];
            return projectList;
        }
        function makeTodoList(defaultUsername, newProjectList) {
            const defaultDates = createDefaultDates()
            const id0 = newProjectList[0].id
            const id1 = newProjectList[1].id

            const todoList = [
                {
                    username: defaultUsername,
                    projectID: id1,
                    name: 'Mitsunobu reaction',
                    desc: 'Book 7 page 12',
                    duedate: defaultDates[0],
                    notes: "Didn't run under nitrogen last time and it was still fine. Repeat reaction to make more starting material",
                },
                {
                    username: defaultUsername,
                    projectID: id0,
                    name: 'Waste Disposal',
                    desc: 'By 10:00 at the latest',
                    duedate: defaultDates[1],
                    notes: 'Take down the TLC plates too',
                },
                {
                    username: defaultUsername,
                    projectID: id0,
                    name: 'Wash glassware',
                    desc: 'Do the syringe needles top',
                    duedate: defaultDates[2],
                    notes: "Make sure to put in the oven in preparation for tomorrow's reaction",
                },
                {
                    username: defaultUsername,
                    projectID: id0,
                    name: 'Waste Solvent Disposal',
                    desc: '10:00 at the lobby',
                    duedate: defaultDates[3],
                    notes: 'Make sure to bring back some empty carboys (if available)',
                },
                {
                    username: defaultUsername,
                    projectID: id1,
                    name: 'BCl3/AlCl3 reaction',
                    desc: 'Book 7 page 10',
                    duedate: defaultDates[4],
                    notes: 'Enough material for a 1g scale reaction',
                },
                {
                    username: defaultUsername,
                    projectID: id1,
                    name: 'Liquid Nitrogen',
                    desc: 'Take it from floor 3 when it arrives',
                    duedate: defaultDates[5],
                    notes: 'Asada has been doing freeze-pump-thaw cycles recently, so need to fill it to the brim',
                },
                {
                    username: defaultUsername,
                    projectID: id1,
                    name: 'Sonogashira coupling',
                    desc: 'Book 7 page 11',
                    duedate: defaultDates[6],
                    notes: 'Use the fresh alkyne when it arrives',
                },
                {
                    username: defaultUsername,
                    projectID: id0,
                    name: 'Dry Ice',
                    desc: 'Move it to the storage container',
                    duedate: defaultDates[7],
                    notes: 'Be sure to dispose of the plastic inside the container before filling it',
                },
                {
                    username: defaultUsername,
                    projectID: id1,
                    name: 'Chemical delivery',
                    desc: 'Materials for the next set of reactions',
                    duedate: defaultDates[8],
                    notes: 'May need to order more AlCl3 soon',
                },
                {
                    username: defaultUsername,
                    projectID: id0,
                    name: 'Fume Hood Maintenance',
                    desc: 'Scheduled maintenance',
                    duedate: defaultDates[9],
                    notes: "Won't be able to use the fume hood on this day",
                },
            ];
            return todoList;
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
                day = day + interval - 30;
                month = month + 1;
            } else {
                day = day + interval;
            }

            const padMonth = month.toString().padStart(2, 0);
            const padDay = day.toString().padStart(2, 0);
            const newDate = `${year}-${padMonth}-${padDay}`;

            return newDate;
        }
        function createDefaultDates() {
            const defaultDates = []
            defaultDates[0] = todayPlusInterval(-1);
            defaultDates[1] = todayPlusInterval(0);
            defaultDates[2] = todayPlusInterval(1);
            defaultDates[3] = todayPlusInterval(2);
            defaultDates[4] = todayPlusInterval(14);
            defaultDates[5] = todayPlusInterval(0);
            defaultDates[6] = todayPlusInterval(0);
            defaultDates[7] = todayPlusInterval(1);
            defaultDates[8] = todayPlusInterval(1);
            defaultDates[9] = todayPlusInterval(4);
            return defaultDates;
        }
        // delete existing 'default' data from the array
        async function findAndDeleteExistingDefaultData(defaultUsername) {
            const projectData = await retrieveDefaultProjectList(defaultUsername);
            if (projectData) {
                await deleteProjectData(projectData);
            }

            const todoData = await retrieveDefaultTodoList(defaultUsername);
            if (todoData) {
                await deleteTodoData(todoData);
            }
        }
        async function retrieveDefaultProjectList(defaultUsername) {
            let uri = `http://localhost:3000/projects?username=${defaultUsername}`;
            const res = await fetch(uri);
            const projectData = await res.json();
            if (projectData.length > 0) {
                return projectData;
            } else {
                return false;
            }
        }
        async function deleteProjectData(projectData) {
            for (let project of projectData) {
                let uri = `http://localhost:3000/projects/${project.id}`;
                const res = await fetch(uri, { method: 'DELETE' });
            }
        }
        async function retrieveDefaultTodoList(defaultUsername) {
            let uri = `http://localhost:3000/todos?username=${defaultUsername}`;
            const res = await fetch(uri);
            const todoData = await res.json();
            if (todoData.length > 0) {
                return todoData;
            } else {
                return false;
            }
        }
        async function deleteTodoData(todoData) {
            for (let todo of todoData) {
                let uri = `http://localhost:3000/todos/${todo.id}`;
                const res = await fetch(uri, { method: 'DELETE' });
            }
        }
        // add the project list to the database
        async function addDefaultProjectListToDB(projectList) {
            for (let project of projectList) {
                let uri = 'http://localhost:3000/projects';
                await fetch(uri, {
                    method: 'POST',
                    body: JSON.stringify(project),
                    headers: { 'Content-Type': 'application/json' },
                });
            }
        }
        // add the todo list to the database
        async function addDefaultTodoListToDB(todoList) {
            for (let todo of todoList) {
                let uri = 'http://localhost:3000/todos';
                await fetch(uri, {
                    method: 'POST',
                    body: JSON.stringify(todo),
                    headers: { 'Content-Type': 'application/json' },
                });
            }
            return true;
        }
        return { setUpDefaultData }
    })()

</script>


<form id="login" on:submit|preventDefault={submitHandler}>
    <img src="./img/BearbeerCrop.png">
    <div class="login-message">Enter Username:</div>
    <input type="text" class="login-input-field" bind:value={username} placeholder="Note: Log in as Default to generate data with dynamic due dates"/>
</form>

<style>

#login {
    display: flex;
    gap: 1rem;
    height: var(--header-height);
    text-align: center;
    padding-left: 4rem;
    /* somehow vertical-align and line-height work together to vertically center the text*/
    vertical-align: middle;
    line-height: var(--header-height);
    background-color: rgba(37, 36, 36, 0.7);
    color: yellow;
    text-shadow: 2px 2px 2px black;
    border: 2px solid black;
}
.login-message {
    font-size: 2rem;
    margin-left: 2rem;
    padding-left: 1rem;
    padding-right: 1rem;
}

.login-input-field:placeholder-shown {
    border: 3px dotted orange;
    font-weight: 800;
    text-align: center;
    color: white;
    text-shadow: none;
    font-size: 1.2rem;
}

.login-input-field {
    flex: 1;
    background-color: transparent;
    font-size: 2rem;
    outline: none;
    border: none;
    margin-right: 6rem;
    padding-left: 1rem;
    min-width: 0px;

    color: yellow;
    text-shadow: 2px 2px 2px black;
}
</style>