<script>
    import NewProject from '$root/components/NewProject.svelte'
    import { createEventDispatcher } from "svelte";

    export let projectListData;
    export let selectedProjectID;
    export let username;

    const dispatch = createEventDispatcher()

    /* NEW PROJECT */
    let newProjectFlag = false;

    const clickNewProject = () => {
        newProjectFlag = true;
        window.addEventListener('mouseup', cancelInputProjectByClick);
    }
    function cancelInputProjectByClick(event) {
        if (event.target != document.querySelector('.proj-input-field')) {
            window.removeEventListener('mouseup', cancelInputProjectByClick);
            newProjectFlag = false
        }
    }

</script>

<ul id="project-list">
    <li class="proj-button-container" on:click={() => selectedProjectID = -1}>
        <div class="project" class:selected={selectedProjectID === -1}>All</div>
    </li>
    {#each projectListData as project (project.id)}
    <li class="proj-button-container" on:click={() => selectedProjectID = project.id}>
        <div class="project" class:selected={selectedProjectID === project.id}>{project.projectName}</div>
        <div class="proj-del-button" on:click={() => dispatch('deleteProject', project)}>✘</div>
    </li>
    {/each}

    <!-- input field for new project when selected -->
    {#if newProjectFlag}
        <NewProject { username } bind:newProjectFlag on:submitProject/>
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
        display: flex;
        align-items: center;
        padding-right: 1.5rem;
    }
    .proj-button-container:hover {
        background-color: blueviolet;
    }
    .project {
        padding-left: 2rem;
        font-size: 2rem;
        flex: 1;
    }
    .selected {
        color: orange;
    }
    .new-project {
        margin-top: 2rem;
        color: greenyellow;
    }
    .proj-del-button {
        font-size: 1.5rem;
        visibility: hidden;
    }
    .proj-del-button:hover {
        color: red;
    }
    /* hover project to show delete button */
    .project:hover + .proj-del-button,
    .proj-del-button:hover {
    visibility: visible;
    }  
</style>