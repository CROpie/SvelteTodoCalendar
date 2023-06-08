<script>
    import CalendarGrid from '$root/components/CalendarGrid.svelte'
    import CalendarData from '$root/components/CalendarData.svelte'
    import CalendarNewTodo from '$root/components/CalendarNewTodo.svelte'

    export let filteredTodoList;
    export let username;
    export let projectListData

    let newTodoFlag = false;
    let clickedDate = ''

    let selectedTodo = {
        username: "",
        name: "",
        duedate: "",
        desc: "",
        notes: "",
        projectID: -1,
        id: -1,
    }



</script>


<div id="calendar-container">
    <CalendarGrid { filteredTodoList } bind:selectedTodo bind:newTodoFlag bind:clickedDate/>
    {#if !newTodoFlag}
    <CalendarData bind:selectedTodo/>
    {:else}
    <CalendarNewTodo { username } { projectListData } bind:clickedDate bind:newTodoFlag on:submitTodo/>
    {/if}
</div>

<style>
    #calendar-container {
    display: flex;
}
</style>