<script>
	import Login from '$root/components/Login.svelte'
  	import Header from '$root/components/Header.svelte'
	import TimePeriodList from '$root/components/TimePeriodList.svelte'
	import ProjectList from '$root/components/ProjectList.svelte'
  	import TodoView from '$root/components/TodoView.svelte'
	import { onMount } from 'svelte'

	/* INITIALIZATION */
	onMount(async () => {
		await getUsernamesFromDB()
	})

	/* LOG IN */
	let loginFlag = false;
	let username = '';

	const loginSuccess = async (event) => {
		username = event.detail
		loginFlag = true;
		await getProjectsFromDB(username)
		await getTodosFromDB(username)
		filterTodoList()
	}

	/* LOG OUT */
	const logout = () => {
		loginFlag = false;

		// reset everything
		username = '';

		projectListData = []
		todoListData = []

		selectedTimePeriod = 'All'
		selectedProjectID = -1
		filteredTodoList = []
	}

  	/* DATABASE */
	let usernameListData = []
  	let projectListData = []
  	let todoListData = []

	// get
	async function getUsernamesFromDB() {
        let uri = 'http://localhost:3000/usernames';
        const res = await fetch(uri);
        const usersData = await res.json();
        usersData.forEach((entry) => {
            usernameListData.push(entry.username);
        });
    }
  	async function getProjectsFromDB() {
        let uri = `http://localhost:3000/projects?username=${username}`;
        const res = await fetch(uri);
        projectListData = await res.json();
  	}
  	async function getTodosFromDB() {
      // uri will include the username entered on login
      let uri = `http://localhost:3000/todos?username=${username}`;
      const res = await fetch(uri);
      todoListData = await res.json();
  	}
	// add
	async function addUsernameToDB(event) {
        const newUsername = event.detail;
        let uri = 'http://localhost:3000/usernames';
        await fetch(uri, {
            method: 'POST',
            body: JSON.stringify(newUsername),
            headers: { 'Content-Type': 'application/json' },
        });

		await getUsernamesFromDB()

		// log in
		loginFlag = true;
		username = newUsername.username;
    }
  	async function addProjectToDB(event) {
		const newProject = event.detail;
        let uri = `http://localhost:3000/projects`;
        await fetch(uri, {
            method: 'POST',
            body: JSON.stringify(newProject),
            headers: { 'Content-Type': 'application/json' },
        });
        getProjectsFromDB()
		// automatically switch to new project: find created project id, set selectedProjectID to it
  	}
	async function addTodoToDB(event) {
		const newTodo = event.detail
		let uri = `http://localhost:3000/todos`;
		await fetch(uri, {
			method: 'POST',
			body: JSON.stringify(newTodo),
			headers: { 'Content-Type': 'application/json' },
		});
		// re-load todos
		await getTodosFromDB()
		filterTodoList()
    }
	// delete
    async function deleteProjectFromDB(event) {
		const project = event.detail
		// delete the project
        let uri = `http://localhost:3000/projects/${project.id}`;
        await fetch(uri, { method: 'DELETE' });
		// delete any todos that have the same todo id
		const associatedTodoIDList = getAssociatedTodoDBIDs(project.id)
		await deleteAssociatedTodosFromDB(associatedTodoIDList)
		console.log(filteredTodoList)

		// retrieve the up-to-date data
		await getProjectsFromDB()
		await getTodosFromDB()

		selectedProjectID = -1;
		filterTodoList()

    }
	function getAssociatedTodoDBIDs(projectID) {
        const associatedTodoIDList = todoListData
        .filter((todo) => todo.projectID == projectID)
        .map((todo) => todo.id);
        return associatedTodoIDList;
    }
	async function deleteAssociatedTodosFromDB(associatedTodoIDList) {
        for (let todoID of associatedTodoIDList) {
            let uri = `http://localhost:3000/todos/${todoID}`;
            await fetch(uri, { method: 'DELETE' });
        }
    }
	async function deleteTodoFromDB(event) {
		const todoID = event.detail
        let uri = `http://localhost:3000/todos/${todoID}`;
        await fetch(uri, { method: 'DELETE' });
		// re-load todos
		await getTodosFromDB()
		filterTodoList()
    }
	// edit
	async function editTodoInDB(event) {
		const editedTodo = event.detail
        let uri = `http://localhost:3000/todos/${editedTodo.id}`;
        await fetch(uri, {
            method: 'PUT',
            body: JSON.stringify(editedTodo),
            headers: { 'Content-Type': 'application/json' },
        });
		// re-load todos
		await getTodosFromDB()
		filterTodoList()
    }

  	/* FILTERING DATA */
	let selectedTimePeriod = 'All'
  	let selectedProjectID = -1
	let filteredTodoList = []

	function filterTodoList() {
        // first, filter by project
        if (selectedProjectID != -1) {
            filteredTodoList = todoListData.filter((todo) => todo.projectID == selectedProjectID);
        } else {
            filteredTodoList = todoListData;
        }
        // then filter by date
        filteredTodoList = filterByDate();
    }
	function filterByDate() {
        // get strings for today's date and a date a week from today
        const today = todayPlusInterval(0);
        const oneWeek = todayPlusInterval(7);

        if (selectedTimePeriod === 'week') {
            filteredTodoList = filteredTodoList.filter((todo) => {
                if (todo.duedate < oneWeek && todo.duedate >= today) {
                    return true;
                }
            });
        } else if (selectedTimePeriod === 'day') {
            filteredTodoList = filteredTodoList.filter((todo) => {
                if (todo.duedate == today) {
                    return true;
                }
            });
        } else if (selectedTimePeriod === 'past') {
            filteredTodoList = filteredTodoList.filter((todo) => {
                if (todo.duedate < today) {
                    return true;
                }
            });
        }
        return filteredTodoList;
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

	// run filterTodoList if either bound variable changes
	$: selectedProjectID && filterTodoList()
	$: selectedTimePeriod && filterTodoList()

</script>

{#if !loginFlag}
	<Login { usernameListData} on:addNewUser={ addUsernameToDB } on:loginSuccess={ loginSuccess }/>
{:else}
	<Header { username } on:logout={logout}/>
{/if}

<div id="full-container">

	<div id="menu">
		<TimePeriodList bind:selectedTimePeriod />
		<ProjectList { username } { projectListData } bind:selectedProjectID on:submitProject={addProjectToDB} on:deleteProject={deleteProjectFromDB} />
	</div>

	<div id="todo-view">
		<TodoView { username } { filteredTodoList } { projectListData } on:submitTodo={addTodoToDB} on:deleteTodo={deleteTodoFromDB} on:editTodo={editTodoInDB}/>
	</div>

</div>

<style>
	#full-container {
		width: 100%;
		height: 100%;
		display: flex;
		font-size: 2rem;
	}
	#menu {
		position: sticky;
		height: 100vh;
		top: 0;
		flex: 0;
		min-width: 350px;
		display: flex;
		flex-direction: column;
		gap: 2rem;
		border: 2px solid black;
		padding-left: 2rem;
		padding-right: 2rem;

		--font-shadow: 2px 2px 2px black;

		/*background-image: url(./Images/3359732.jpg);*/
		background-size: 100% 100%;

		background-color: grey;
	}
	#todo-view {
		flex: 1;
		border: 2px solid black;
		min-width: 450px;
		/*background-image: url(./Images/3626461.jpg);*/
		background-size: cover;
		background-repeat: no-repeat;
		background-attachment: fixed;
		background-position: 200px;
		background-color: grey;
	}

</style>