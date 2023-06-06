<script>
  	import Header from '$root/components/Header.svelte'
	import ProjectList from '$root/components/ProjectList.svelte'
  	import TodoView from '$root/components/TodoView.svelte'

  	// database functions
  	let projectListData = []
  	let todoListData = []

  	async function getProjectsFromDB() {
        let uri = `http://localhost:3000/projects?username=default`;
        const res = await fetch(uri);
        projectListData = await res.json();
  	}

  	async function getTodosFromDB() {
      // uri will include the username entered on login
      let uri = `http://localhost:3000/todos?username=default`;
      const res = await fetch(uri);
      todoListData = await res.json();
	  filterTodoList()
  	}

  	async function addProjectToDB(newProject) {
        let uri = `http://localhost:3000/projects`;
        await fetch(uri, {
            method: 'POST',
            body: JSON.stringify(newProject),
            headers: { 'Content-Type': 'application/json' },
        });
        getProjectsFromDB()
  	}

	async function addTodoToDB(newTodo) {
		let uri = `http://localhost:3000/todos`;
		await fetch(uri, {
			method: 'POST',
			body: JSON.stringify(newTodo),
			headers: { 'Content-Type': 'application/json' },
		});
		getTodosFromDB()
    }

  	// get data on initialization
  	getProjectsFromDB()
  	getTodosFromDB()

  	// handle adding a new project
  	const submitProject = (event) => {
    	const newProjectName = event.detail
    	const newProject = {
      	username: 'default',
      	projectName: newProjectName,
		}

    	addProjectToDB(newProject)
  	}

	// handle adding a new todo
	const submitTodo = (event) => {
    	const newTodo = event.detail
    	addTodoToDB(newTodo)
  	}
  
  	// filter functions
  	let selectedProjectID = -1
	let filteredTodoList = []

	const selectProject = (event) => {
		selectedProjectID = event.detail
		filterTodoList()
	}

	function filterTodoList() {
        // first, filter by project
        if (selectedProjectID != -1) {
            filteredTodoList = todoListData.filter((todo) => todo.projectID == selectedProjectID);
        } else {
            filteredTodoList = todoListData;
        }
        // then filter by date
        // prettier-ignore
        // filteredTodoList = filterByDate(filteredTodoList, filterSettings.dateIndex);
    }

</script>

<Header />
<div id="full-container">

	<div id="menu">
		<ProjectList { projectListData } { selectedProjectID } on:selectProject={selectProject} on:submitProject={submitProject}/>
	</div>

	<div id="todo-view">
		<TodoView { filteredTodoList } { projectListData } on:submitTodo={submitTodo}/>
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