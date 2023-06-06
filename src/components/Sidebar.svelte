<script>
    import ProjectList from '$root/components/ProjectList.svelte'

    export let projectListData;

    /* project selection */
    let selectedProjectID = -1 // start with All selected as default

    // handling the dispatched event
    const selectProject = (event) => {
        selectedProjectID = event.detail
    }
    
  </script>

<div id="menu">
    <ProjectList { projectListData } { selectedProjectID } on:selectProject={selectProject} on:submitProject/>
</div>

<style>
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
</style>