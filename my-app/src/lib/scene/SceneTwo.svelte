<script>
    import { T, useFrame } from '@threlte/core';
    import { OrbitControls, Sky, interactivity } from '@threlte/extras';

    interactivity();

    let orientationViewToggle = false;
    let clickCounter = 0;
    $: targetX = 0;
    $: targetY = 0;
    $: targetZ = 0;
    let orientX = 0;
    let orientY = 0;
    let orientZ = 0;
    const rotationSpeed = 0.05; // Adjust for desired smoothness

    function resetAxes() {
        // Reset the target rotation to the current rotation
        targetX = orientX;
        targetY = orientY;
        targetZ = orientZ;
    }

    function toggleOrientationView() {
        orientationViewToggle = !orientationViewToggle;
        clickCounter += 1;
        console.log('orientation view toggle clicked number of times ' + clickCounter);
    }

    function rotateX(){
        resetAxes();
        targetX += Math.PI / 2;
        console.log('orientX')
    }

    function rotateY(){
        resetAxes();
        targetY += Math.PI / 2;
        console.log('orientY')
    }

    function rotateZ(){
        resetAxes();
        targetZ += Math.PI / 2;
        console.log('orientZ')
    }

    useFrame(() => {
        // Smoothly interpolate rotation values towards the target
        orientX += (targetX - orientX) * rotationSpeed;
        orientY += (targetY - orientY) * rotationSpeed;
        orientZ += (targetZ - orientZ) * rotationSpeed;
    });
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0}/>
</T.PerspectiveCamera>

<Sky />

<T.Mesh position={[0, 1, 0]} rotation={[orientX, orientY, orientZ]} on:click={() => {toggleOrientationView()}}>
    <T.TorusKnotGeometry />
    <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
</T.Mesh>

{#if orientationViewToggle}
    <T.Mesh position={[0, 5, 0]} rotation={[Math.PI / 2, 0, 0]} on:click={() => {rotateX()}}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>

    <T.Mesh position={[0, 0, 5]} rotation={[Math.PI / 2, 0, - Math.PI / 2]} on:click={() => {rotateY()}}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>

    <T.Mesh position={[5, 0, 0]} rotation={[Math.PI, 0, Math.PI]} on:click={() => {rotateZ()}}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>
{/if}


<T.GridHelper />