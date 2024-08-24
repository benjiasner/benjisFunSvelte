<script>
    import { T } from '@threlte/core';
    import { OrbitControls, TransformControls, Sky } from '@threlte/extras';
    import * as THREE from 'three';

    let torusMesh;
    
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0}/>
</T.PerspectiveCamera>

<Sky />

<TransformControls 
    mode="rotate"
    rotationSnap={Math.PI / 2}
    size={2}
    position={[0,3,0]}
>
    <T.Mesh bind:ref={torusMesh}
            position={[0, -1, 0]}>
        <T.TorusKnotGeometry />
        <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
    </T.Mesh>
</TransformControls>

<T.GridHelper />