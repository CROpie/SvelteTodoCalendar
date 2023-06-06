<script>
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
    {/if}

    <li class="proj-button-container new-project" on:click={clickNewProject}>
        <div class="project">New Project</div>
    </li>

</ul>

<style>
    /* project list */
    #project-list {
        list-style-type: none;
    }
    .proj-button-container {
        cursor: pointer;
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

    /* new project */
    .new-project {
        margin-top: 2rem;
        color: greenyellow;
    }
    .proj-input-field {
        max-width: 100%;
        color: white;
        background-color: transparent;
        box-shadow: 0 0 10px #9ecaed;
    }
</style>