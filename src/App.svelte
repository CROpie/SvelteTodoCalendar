<script>
  import Header from '$root/components/Header.svelte'
  import Sidebar from '$root/components/Sidebar.svelte'
  import TodoView from '$root/components/TodoView.svelte'

  let projectListData = [
    { name: "Lab Duties",
      id: 1,
    },
    { name: "Catalysis",
      id: 2,
    },]
    $: console.log(projectListData)

    // handle adding a new project
    const submitProject = (event) => {
      const newProjectName = event.detail
      const newProject = {
        name: newProjectName,
        id: Math.random()
      }
      projectListData = [...projectListData, newProject]
    }

</script>

<Header />
<div id="full-container">
  <Sidebar {projectListData} on:submitProject={submitProject}/>
  <TodoView />
</div>

<style>
  #full-container {
      width: 100%;
      height: 100%;
      display: flex;
      font-size: 2rem;
  }

</style>