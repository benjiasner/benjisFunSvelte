<script>
    import { Canvas } from "@threlte/core";
    import SceneFive from '../../lib/scene/SceneFive.svelte';
    import Menu from '../../lib/components/Menu.svelte';
    import { onMount } from 'svelte';

    let isMenuOpen = false;

    const toggleMenu = () => {
        isMenuOpen = !isMenuOpen;
    };
</script>

<main>
    <div class="container {isMenuOpen ? 'menu-open' : ''}">
        <Menu isOpen={isMenuOpen} />
        <div class="canvas-wrapper">
            <Canvas>
                <SceneFive />
            </Canvas>
        </div>
    </div>
    <!-- Add a button to toggle the menu -->
    <button class="toggle-button {isMenuOpen ? 'menu-open' : ''}" on:click={toggleMenu}>
        {isMenuOpen ? '<' : '>'}
    </button>
</main>

<style>
    /* Ensure the body and html elements fill the entire viewport */
    html, body {
        margin: 0;
        padding: 0;
        height: 100%;
        width: 100%;
        overflow: hidden;
    }

    /* Main should fill the entire viewport */
    main {
        height: 100vh; /* Full viewport height */
        width: 100vw;  /* Full viewport width */
    }

    /* Flex container for Menu and Canvas */
    .container {
        display: flex;
        height: 100%;
        width: 100%;
    }

    /* Menu styles */
    .menu {
        width: 0; /* Default width when closed */
        transition: width 0.3s ease-in-out;
        background-color: rgba(0, 0, 0, 0.8);
        color: white;
        overflow: hidden;
    }

    .menu.open {
        width: 350px; /* Width when open */
    }

    /* Canvas wrapper styles */
    .canvas-wrapper {
        flex: 1;
        height: 100%;
        transition: width 0.3s ease-in-out;
    }

    /* Adjust canvas width when menu is open */
    .container.menu-open .canvas-wrapper {
        width: calc(100% - 350px); /* Shrink canvas to make space for the menu */
    }

    /* Add styles for the toggle button */
    .toggle-button {
        position: absolute;
        top: 20px;
        left: 20px; /* Default position when menu is closed */
        z-index: 100;
        padding: 10px;
        background-color: rgba(0, 0, 0, 0.8);
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: left 0.3s ease-in-out; /* Smooth transition for button movement */
    }

    /* Move the button to the right of the menu when open */
    .toggle-button.menu-open {
        left: 370px; /* 400px (menu width) + 20px (margin) */
    }

    .toggle-button:hover {
        background-color: rgba(0, 0, 0, 0.5);
    }
</style>
