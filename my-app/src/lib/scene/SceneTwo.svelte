<script>
    import { T, useFrame } from '@threlte/core';
    import { OrbitControls, Sky, interactivity, Stars } from '@threlte/extras';
    import * as THREE from 'three';

    interactivity();

    let orientationViewToggle = false;
    let clickCounter = 0;
    let targetX = 0;
    let targetY = 0;
    let targetZ = 0;
    let orientX = 0;
    let orientY = 0;
    let orientZ = 0;
    let posY = 0;
    let colorTorus = 'gray';
    let colorsArrows = ['gray', 'gray', 'gray'];
    const rotationSpeed = 0.1; // Adjust for desired smoothness
    let clickToggle = true;
    var meshHolder;
    let clickCooldown = false;

    // Reference to the torus mesh
    let torusMesh;
    let boundingBox;
    
    function resetAxes() {
        // Reset the target rotation to the current rotation
        targetX = orientX;
        targetY = orientY;
        targetZ = orientZ;
    }

    function toggleOrientationView() {
    if (!clickCooldown) {
        orientationViewToggle = !orientationViewToggle;
        clickCounter += 1;
        console.log('orientation view toggle clicked number of times ' + clickCounter);
        clickCooldown = true;
        setTimeout(() => {
            clickCooldown = false;
        }, 300); // Cooldown period in milliseconds
    }
}

    function rotateX(){
        if (clickToggle && torusMesh) {
            resetAxes();
            targetX += Math.PI / 2;
            clickToggle = false; // Prevent further clicks until rotation completes
            console.log('rotateX');
        }
    }

    function rotateY(){
        if (clickToggle && torusMesh) {
            resetAxes();
            targetY += Math.PI / 2;
            clickToggle = false; // Prevent further clicks until rotation completes
            console.log('rotateY');
        }
    }

    function rotateZ(){
        if (clickToggle && torusMesh) {
            resetAxes();
            targetZ += Math.PI / 2;
            clickToggle = false; // Prevent further clicks until rotation completes
            console.log('rotateZ');
        }
    }

    function hoverOnObject(){
        colorTorus = 'blue';
        console.log('hovered on torus');
    }

    function hoverOffObject(){
        colorTorus = 'gray';
        console.log('hovered off torus');
    }

    function hoverOnArrow(arrowIndex){
        colorsArrows[arrowIndex] = 'blue';
        console.log('hovered on arrow');
    }

    function hoverOffArrow(arrowIndex){
        colorsArrows[arrowIndex] = 'gray';
        console.log('hovered off arrow');
    }

    function applyUpdates() {
        meshHolder = new THREE.Object3D;
        meshHolder.add(torusMesh);
        //apply rotations
        torusMesh = meshHolder;
    }

    useFrame(() => {        
        // Smoothly interpolate rotation values towards the target
        orientX += (targetX - orientX) * rotationSpeed;
        orientY += (targetY - orientY) * rotationSpeed;
        orientZ += (targetZ - orientZ) * rotationSpeed;

        // Check if rotation is complete
        if (Math.abs(orientX - targetX) < 0.01 &&
            Math.abs(orientY - targetY) < 0.01 &&
            Math.abs(orientZ - targetZ) < 0.01) {
            orientX = targetX;
            orientY = targetY;
            orientZ = targetZ;

            clickToggle = true; // Allow next rotation
        }

        // Update torusMesh's matrixWorld and adjust position
        if (torusMesh) {
            torusMesh.rotation.set(orientX, orientY, orientZ);
            torusMesh.updateMatrixWorld();

            // Compute and adjust the bounding box
            if (torusMesh.geometry) {
                torusMesh.geometry.computeBoundingBox();
                boundingBox = torusMesh.geometry.boundingBox.clone().applyMatrix4(torusMesh.matrixWorld);

                // Calculate the offset needed to move the lowest point to y = 0
                const minY = boundingBox.min.y;
                const offsetY = -minY;

                // Adjust the torus position
                posY += offsetY;
            }
        }
    });
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0}/>
</T.PerspectiveCamera>

<Sky />

<T.Group>
    <T.Mesh bind:ref={torusMesh}
            position={[0, posY, 0]} 
            rotation={[orientX, orientY, orientZ]} 
            on:click={toggleOrientationView}
            on:pointerover={hoverOnObject}
            on:pointerout={hoverOffObject}>
        <T.TorusKnotGeometry />
        <T.MeshStandardMaterial roughness={0} color={colorTorus} metalness={'1'}/>
    </T.Mesh>
</T.Group>

{#if orientationViewToggle}
    <T.Mesh position={[0, 5, 0]} 
            rotation={[Math.PI / 2, 0, 0]} 
            on:click={rotateX}
            on:pointerover={() => hoverOnArrow(0)}
            on:pointerout={() => hoverOffArrow(0)}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={colorsArrows[0]} metalness={'1'}/>
    </T.Mesh>

    <T.Mesh position={[0, 0, 5]} 
            rotation={[Math.PI / 2, 0, - Math.PI / 2]} 
            on:click={rotateY}
            on:pointerover={() => hoverOnArrow(1)}
            on:pointerout={() => hoverOffArrow(1)}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={colorsArrows[1]} metalness={'1'}/>
    </T.Mesh>

    <T.Mesh position={[5, 0, 0]} 
            rotation={[Math.PI, 0, Math.PI]} 
            on:click={rotateZ}
            on:pointerover={() => hoverOnArrow(2)}
            on:pointerout={() => hoverOffArrow(2)}>
        <T.ConeGeometry args={[.2, 1, 40]}/>
        <T.MeshStandardMaterial roughness={0} color={colorsArrows[2]} metalness={'1'}/>
    </T.Mesh>
{/if}

<T.GridHelper />

