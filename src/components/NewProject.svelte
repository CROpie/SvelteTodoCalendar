<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher()

    export let username;
    export let newProjectFlag;

    let newProject = {
            username: username,
            projectName: '',
    }

    /* event handlers */
    const submitHandler = () => {
        dispatch('submitProject', newProject)
        newProjectFlag = false; // remove <NewProject /> from the DOM
    }

</script>

<form on:submit|preventDefault={submitHandler} class="proj-button-container">
    <input class="project proj-input-field" bind:value={newProject.projectName} autofocus>
</form>

<style>

    /* new project */
    .project {
        padding-left: 2rem;
        font-size: 2rem;
    }
    .proj-input-field {
        max-width: 100%;
        color: white;
        background-color: transparent;
        box-shadow: 0 0 10px #9ecaed;
    }
</style>