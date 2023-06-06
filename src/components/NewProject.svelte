<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher()
</script>

<div id="new-project-container" on:click={() => dispatch('clickNewProject')}>
    <div id="new-project">New Project</div>
</div>

<style>
    #new-project {
    color: greenyellow;
    padding-left: 2rem;
    }

    #new-project:hover {
        background-color: blueviolet;
    }
</style>