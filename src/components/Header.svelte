<script>
    import { createEventDispatcher } from "svelte";
    export let username

    const dispatch = createEventDispatcher()
</script>

<div id="header">
    <img src="./img/BearbeerCrop.png">
    <div class="header-message">Hello, {username}!</div>
    <button class="logout-button" on:click={() => dispatch('logout')}>LOG OUT</button>
</div>

<style>

#header {
    display: flex;
    justify-content: space-between;
    width: 100%;
    padding-left: 4rem;
    height: var(--header-height);
    text-align: center;

    /* somehow vertical-align and line-height work together to vertically center the text*/
    vertical-align: middle;
    line-height: var(--header-height);

    background-color: rgba(37, 36, 36, 0.7);
    color: yellow;
    text-shadow: 2px 2px 2px black;

    border: 2px solid black;
}
.header-message {
    font-size: 2rem;
    margin-left: 2rem;
    padding-left: 1rem;
    padding-right: 1rem;
}
.logout-button {
    padding: 1rem;
    margin-right: 1rem;
    background-color: transparent;

    color: white;
    text-shadow: 2px 2px 2px black;
}

.logout-button:hover {
    color: black;
    text-shadow: 2px 2px 2px white;
}
</style>