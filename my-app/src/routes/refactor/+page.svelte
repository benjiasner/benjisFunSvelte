<script>
    import RefactorScene from "$lib/scene/RefactorScene.svelte";
    import { Canvas } from "@threlte/core";
</script>

<main>
    <h1>Using Transform Controls</h1>
    <div class="canvas-wrapper">
        <Canvas>
            <RefactorScene />
        </Canvas>
    </div>
</main>

<style>
    .canvas-wrapper {
        height: 90vh;
        width: 99vw;
    }
</style>
