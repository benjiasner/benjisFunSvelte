<script>
    import Search from '../components/MenuComponents/Search.svelte'
    import Home from '../components/MenuComponents/Home.svelte'
    import Library from '../components/MenuComponents/Library.svelte'
    export let isOpen = false;
    let menuItem = 'Home';

    function toggleMenuItem(newItem) {
        menuItem = newItem;
    };
</script>

<div class="menu {isOpen ? 'open' : ''}">

    {#if menuItem === 'Home'}
        <Home />
    {:else if menuItem === 'Search'}
        <Search />
    {:else if menuItem === 'Library'}
        <Library />
    {:else if menuItem === 'Devices'}

    {:else if menuItem === 'Profile'}

    {/if}
    <!-- Buttons at the bottom of the menu -->
    <div class="buttons">
        <button on:click={() => toggleMenuItem('Home')}>Home</button>
        <button on:click={() => toggleMenuItem('Search')}>Search</button>
        <button on:click={() => toggleMenuItem('Library')}>Library</button>
        <button on:click={() => toggleMenuItem('Devices')}>Devices</button>
        <button on:click={() => toggleMenuItem('Profile')}>Profile</button>
    </div>
</div>

<style>
    .menu {
        width: 0;
        height: 100%;
        transition: width 0.3s ease-in-out;
        background-color: rgba(0, 0, 0, 0.8);
        color: white;
        overflow: hidden;
        display: flex;
        flex-direction: column;
    }

    .menu.open {
        width: 400px;
    }

    .buttons {
        margin-top: auto; /* Push buttons to the bottom */
        padding: 20px;
        display: flex;
        flex-direction: row;
        gap: 10px; /* Space between buttons */
    }

    .buttons button {
        padding: 10px;
        background-color: #444;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        text-align: left;
    }

    .buttons button:hover {
        background-color: #555;
    }
</style>