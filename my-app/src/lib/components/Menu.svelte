<script>
    export let isOpen = false;
</script>

<div class="menu {isOpen ? 'open' : ''}">


    <!-- Buttons at the bottom of the menu -->
    <div class="buttons">
        <button>Home</button>
        <button>Search</button>
        <button>Library</button>
        <button>Devices</button>
        <button>Profile</button>
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
        width: 350px;
    }

    .buttons {
        margin-top: auto; /* Push buttons to the bottom */
        padding: 10px;
        display: flex;
        flex-direction: row;
        gap: 7px; /* Space between buttons */
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