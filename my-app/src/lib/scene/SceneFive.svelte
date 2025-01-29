<script>
    import { T, useLoader } from '@threlte/core';
    import { OrbitControls, Sky } from '@threlte/extras';
    import { STLLoader } from 'three/examples/jsm/loaders/STLLoader';

    // Use the STLLoader with useLoader
    const { load } = useLoader(STLLoader);

    // Load the STL model
    let model;
    load('/3dModel/crown.STL').then((geometry) => {
        model = geometry; // Store the loaded geometry
    });
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0.5} />
</T.PerspectiveCamera>

<Sky />

<!-- Render the loaded model -->
{#if model}
    <T.Mesh geometry={model} scale={0.05}>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={1} />
    </T.Mesh>
{/if}

<T.GridHelper />