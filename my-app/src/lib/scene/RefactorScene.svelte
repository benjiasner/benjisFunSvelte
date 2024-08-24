<script lang="ts">
    import { T, useTask } from "@threlte/core";
    import { Float, OrbitControls, Sky, interactivity } from "@threlte/extras";
    import { Theatre } from "@threlte/theatre";
    import { onMount } from "svelte";
    import { AxesHelper, Quaternion, Vector3, type Mesh } from "three";
    interactivity();
    let x_axis_button: Mesh;
    let y_axis_button: Mesh;
    let z_axis_button: Mesh;
    let object: Mesh;
    let button_disabled: false;
    onMount(() => {
        object.matrixAutoUpdate = true;
    });

    function formatQuaterion(q: Quaternion) {
        return `${q.x}, ${q.y}, ${q.z}, ${q.w}`;
    }

    type Rotation = {
        radianAngle: number;
        axis: Vector3;
    };

    let rotationQueue: Rotation[] = [];

    function click_x() {
        // +z world axis rotation
        console.log("click_x");

        rotationQueue = [
            {
                radianAngle: Math.PI / 2,
                axis: new Vector3(0, 0, 1),
            },
            ...rotationQueue,
        ];
    }
    function click_y() {
        console.log("click_y");
        rotationQueue = [
            {
                radianAngle: Math.PI / 2,
                axis: new Vector3(1, 0, 0),
            },
            ...rotationQueue,
        ];
    }
    function click_z() {
        console.log("click_z");
        rotationQueue = [
            {
                radianAngle: Math.PI / 2,
                axis: new Vector3(0, 1, 0),
            },
            ...rotationQueue,
        ];
    }

    useTask((frameTime) => {
        const curr_rotation_to_animate = rotationQueue.pop();
        if (curr_rotation_to_animate !== undefined) {
        }
    });
</script>

<Sky />
<T.Mesh bind:ref={object}>
    <T.TorusKnotGeometry />
    <T.MeshStandardMaterial roughness={0} color={0xff0000} metalness={"1"} />
</T.Mesh>

<T.PerspectiveCamera makeDefault position={[0, 20, 20]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0} />
</T.PerspectiveCamera>
<T.GridHelper />
<T.AxesHelper scale={100} />

<T.Mesh position={[5, 0, 0]} bind:ref={x_axis_button} on:click={click_x}>
    <T.ConeGeometry args={[0.2, 1, 40]} />
    <T.MeshStandardMaterial roughness={0} color={0x0000ff} metalness={"1"} />
</T.Mesh>

<T.Mesh
    position={[0, 5, 0]}
    rotation={[Math.PI / 2, 0, 0]}
    bind:ref={y_axis_button}
    on:click={click_y}
>
    <T.ConeGeometry args={[0.2, 1, 40]} />
    <T.MeshStandardMaterial roughness={0} color={0x0000ff} metalness={"1"} />
</T.Mesh>

<T.Mesh
    position={[0, 0, 5]}
    rotation={[Math.PI / 2, 0, -Math.PI / 2]}
    bind:ref={z_axis_button}
    on:click={click_z}
>
    <T.ConeGeometry args={[0.2, 1, 40]} />
    <T.MeshStandardMaterial roughness={0} color={0x0000ff} metalness={"1"} />
</T.Mesh>
