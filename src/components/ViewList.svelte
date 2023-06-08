<script>
    export let selectedView
</script>

<ul id="view-option-list">
    <li class="view-option-container" on:click={() => selectedView = 'List'}>
        <div class="view-option" class:selected={selectedView === 'List'}>List View</div>
    </li>
    <li class="view-option-container" on:click={() => selectedView = 'Calendar'}>
        <div class="view-option" class:selected={selectedView === 'Calendar'}>Calendar View</div>
    </li>
</ul>

<style>
#view-option-list {
    padding-top: 1rem;
    list-style-type: none;
    color: white;
    text-shadow: 2px 2px 2px black;
}

.view-option-container {
    cursor: pointer;
}

.view-option-container:hover {
    background-color: blueviolet;
}
.view-option {
    padding-left: 2rem;
    font-size: 2rem;
}
.selected {
        color: orange;
    }
</style>