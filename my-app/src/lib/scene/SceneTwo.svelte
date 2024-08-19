<script>
    import { T } from '@threlte/core';
    import { OrbitControls, Sky } from '@threlte/extras';
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0}/>
</T.PerspectiveCamera>

<Sky />

<T.Mesh position={[0, 1, 0]} >
    <T.TorusKnotGeometry/>
    <T.MeshStandardMaterial roughness={0} color={'gray'} metalness={'1'}/>
</T.Mesh>

<T.GridHelper />