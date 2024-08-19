<script>
    import { T } from '@threlte/core';
    import { OrbitControls, Sky, interactivity } from '@threlte/extras';

    interactivity();

    let orientationViewToggle = false;
    $: orientX = 0;
    $: orientY = 0;

    function toggleOrientationView() {
        orientationViewToggle = !orientationViewToggle;
        console.log('orientation view toggle clicked')
    }

    function rotateX(){
        orientX = orientX + Math.PI / 2;
        console.log('orientX')
    }

    function rotateY(){
        orientY = orientY + Math.PI / 2;
        console.log('orientY')
    }
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0}/>
</T.PerspectiveCamera>

<Sky />

<T.Mesh position={[0, 1, 0]} rotation={[orientY, 0, orientX]} on:click={() => {toggleOrientationView()}}>
    <T.TorusKnotGeometry />
    <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
</T.Mesh>

{#if orientationViewToggle}
    <T.Mesh position={[0, 5, 0]} rotation={[Math.PI / 2, 0, 0]} on:click={() => {console.log('arrow clicked z')}}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>

    <T.Mesh position={[0, 0, 5]} rotation={[Math.PI / 2, 0, - Math.PI / 2]} on:click={() => {rotateX()}}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>

    <T.Mesh position={[5, 0, 0]} rotation={[Math.PI, 0, Math.PI]} on:click={() => {rotateY()}}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>
{/if}


<T.GridHelper />