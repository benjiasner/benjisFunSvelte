<script>
    import { T } from '@threlte/core';
    import { OrbitControls, 
            TransformControls, 
            Sky, 
            interactivity } from '@threlte/extras';
    import * as THREE from 'three';

    //TODO
    // - add centering after each move
    // - figure out the y=0 problem
    // - 

    interactivity();

    let torusMesh;
    let clickCooldown = false;
    let transformCntlsEnabled = false;
    let objectColor;
    let hoverOnTransCntls = 

    function clickOnObject(){
        if (!clickCooldown){
            transformCntlsEnabled = !transformCntlsEnabled;
            console.log('clicked on object')
            clickCooldown = true;
            setTimeout(() => {
                clickCooldown = false;
            }, 300); // Cooldown period in milliseconds
        }
    }

    function hoverOnObject(){
        objectColor="blue";
        console.log('hovered on object');
    }

    function hoverOffObject(){
        objectColor="gray"
        console.log('hovered off object')
    }

    function hoverOnTransformControls() {
        
        console.log('hovered on transform controls')
    }
    
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0}/>
</T.PerspectiveCamera>

<Sky />

<TransformControls 
    mode="rotate"
    rotationSnap={Math.PI / 2}
    size={1}
    position={[0,0,0]}
    enabled={transformCntlsEnabled}
    showX={transformCntlsEnabled}
    showY={transformCntlsEnabled}
    showZ={transformCntlsEnabled}
    on:pointerover={hoverOnTransformControls}
>

    <T.Mesh bind:ref={torusMesh}
            position={[0, 0, 0]}
            on:click={clickOnObject}
            on:pointerover={hoverOnObject}
            on:pointerout={hoverOffObject}
    >
        <T.TorusKnotGeometry />
        <T.MeshStandardMaterial roughness={0} color={objectColor} metalness={'1'}/>
    </T.Mesh>

</TransformControls>

<T.GridHelper />