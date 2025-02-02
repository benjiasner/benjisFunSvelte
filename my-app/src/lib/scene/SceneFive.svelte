<script>
    import { T, useLoader } from '@threlte/core';
    import { OrbitControls, Sky } from '@threlte/extras';
    import { STLLoader } from 'three/examples/jsm/loaders/STLLoader';
    import { selectedFileEntityId } from '../stores'

    // Use the STLLoader with useLoader

    let model;

    // $: {
    //     console.log($selectedFileEntityId);
    // }

    $: if ($selectedFileEntityId) {
        const { load } = useLoader(STLLoader);
        load(`https://benjisfunbackend.onrender.com/api/get-3d-file/${$selectedFileEntityId}`).then(loadedModel => {
            model = loadedModel;
        }).catch(error => {
            console.error(`Failed to load model: /${$selectedFileEntityId}`, error);
        });
    }

    // Load the STL model
    // load('/3dModel/crown.STL').then((geometry) => {
    //     model = geometry; // Store the loaded geometry
    // });
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