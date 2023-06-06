<script>
    import NewProject from '$root/components/NewProject.svelte'

    import { createEventDispatcher } from "svelte";
    export let projectListData;
    export let selectedProjectID;

    const dispatch = createEventDispatcher()

    /* Creating a new project */
    let newProject = false;
    let newProjectName = ''

    const clickNewProject = () => {
        newProject = true;
        window.addEventListener('mouseup', cancelInputProjectByClick);
    }

    function cancelInputProjectByClick(event) {
        if (event.target != document.querySelector('.proj-input-field')) {
            window.removeEventListener('mouseup', cancelInputProjectByClick);
            newProject = false
            newProjectName = ''
        }
    }

    const submitHandler = () => {
        dispatch('submitProject', newProjectName)
        newProject = false;
        newProjectName = '';
    }

</script>

<ul id="project-list">
    <li class="proj-button-container" on:click={() => dispatch('selectProject', -1)}>
        <div class="project" class:selected={selectedProjectID === -1}>All</div>
    </li>
    {#each projectListData as project}
    <li class="proj-button-container" on:click={() => dispatch('selectProject', project.id)}>
        <div class="project" class:selected={selectedProjectID === project.id}>{project.name}</div>
    </li>
    {/each}

    <!-- input field for new project when selected -->
    {#if newProject}
    <form on:submit|preventDefault={submitHandler} class="proj-button-container">
        <input class="project proj-input-field" bind:value={newProjectName} autofocus>
    </form>
    <!-- put in some sort of spacer to stop 'New project' moving down? some sort of css instead? 
    {:else}
    <li class="proj-button-container">
        <div class="project spacer">space</div>
    </li> -->
    {/if}

</ul>



<NewProject on:clickNewProject={clickNewProject}/>

<style>
    #project-list {
        list-style-type: none;
    }
    .proj-button-container:hover {
        background-color: blueviolet;
    }

    .project {
        padding-left: 2rem;
        font-size: 2rem;
    }

    .selected {
        color: orange;
    }

    /* click new project */
    .proj-input-field {
        max-width: 100%;
        color: white;
        background-color: transparent;
        box-shadow: 0 0 10px #9ecaed;
    }
</style>