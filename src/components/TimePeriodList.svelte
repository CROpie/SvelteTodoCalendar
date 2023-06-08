<script>

    export let selectedTimePeriod;

</script>

<ul id="time-period-list">
    <li class="time-period-button-container" on:click={() => selectedTimePeriod = 'All'}>
        <div class="time-period" class:selected={selectedTimePeriod === 'All'}>All</div>
    </li>
    <li class="time-period-button-container" on:click={() => selectedTimePeriod = 'week'}>
        <div class="time-period" class:selected={selectedTimePeriod === 'week'}>7 Days</div>
    </li>
    <li class="time-period-button-container" on:click={() => selectedTimePeriod = 'day'}>
        <div class="time-period" class:selected={selectedTimePeriod === 'day'}>Today</div>
    </li>
    <li class="time-period-button-container" on:click={() => selectedTimePeriod = 'past'}>
        <div class="time-period" class:selected={selectedTimePeriod === 'past'}>Past</div>
    </li>
</ul>

<style>
    #time-period-list {
        list-style-type: none;
        color: white;
        text-shadow: 2px 2px 2px black;
    }
    .time-period-button-container {
        cursor: pointer;
    }
    .time-period-button-container:hover {
        background-color: blueviolet;
    }
    .time-period {
        padding-left: 2rem;
        font-size: 2rem;
    }
    .selected {
        color: orange;
    }
</style>