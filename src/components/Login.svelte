<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher()

    export let usernameListData;

    let username = ''

    const submitHandler = () => {
        if (!username) {
            return
        } else {
            if (!usernameListData.includes(username)) {
                console.log("username not recognized. Creating new entry.")
                const newUsername = { username }
                dispatch('addNewUser', newUsername)
            } else {
                dispatch('loginSuccess', username)
            }
        }
    }

</script>


<form id="login" on:submit|preventDefault={submitHandler}>
    <div class="login-message">Enter Username:</div>
    <input type="text" class="login-input-field" bind:value={username} placeholder="Note: Log in as Default to generate data with dynamic due dates"/>
</form>

<style>

#login {
    display: flex;
    gap: 1rem;

    height: var(--header-height);
    text-align: center;
    margin-left: 350px;

    /* somehow vertical-align and line-height work together to vertically center the text*/
    vertical-align: middle;
    line-height: var(--header-height);

    background-color: rgba(211, 211, 211, 0.7);
    color: yellow;
    text-shadow: 2px 2px 2px black;

    border: 2px solid black;
}

.login-input-field:placeholder-shown {
    border: 3px dotted orange;
    font-weight: 800;
    text-align: center;
    color: purple;
    text-shadow: none;
    font-size: 1.2rem;
}

.login-input-field {
    flex: 1;
    background-color: transparent;
    font-size: 2rem;
    outline: none;
    border: none;
    margin-right: 6rem;
    padding-left: 1rem;
    min-width: 0px;

    color: yellow;
    text-shadow: 2px 2px 2px black;
}
</style>