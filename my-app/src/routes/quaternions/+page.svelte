<script>
    import { Canvas } from "@threlte/core";
    import SceneFour from '../../lib/scene/SceneFour.svelte';
</script>

<main>
    <h1>Using QUATERNIONS to orient a Mesh in THRELTE</h1>
    <div class="canvas-wrapper">
        <Canvas>
            <SceneFour />
        </Canvas>
    </div>
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
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        position: relative;
    }

    /* Ensure the canvas-wrapper fills the remaining space */
    .canvas-wrapper {
        flex: 1;
        height: 100%;
        width: 100%;
    }

    h1 {
        position: absolute;
        top: 20px;
        left: 20px;
        color: white;
        z-index: 10;
        margin: 0;
    }
</style>
