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
        rot: Quaternion;
    };

    let rotationQueue: Rotation[] = [];
    type RotationParams = {
        start_q: Quaternion;
        end_q: Quaternion;
    };

    let curr_rotation_params: RotationParams | undefined = undefined;

    const quaternionX = new Quaternion().setFromAxisAngle(
        new Vector3(1, 0, 0),
        Math.PI / 2,
    );
    const quaternionY = new Quaternion().setFromAxisAngle(
        new Vector3(0, 1, 0),
        Math.PI / 2,
    );
    const quaternionZ = new Quaternion().setFromAxisAngle(
        new Vector3(0, 0, 1),
        Math.PI / 2,
    );

    function rotateX() {
        if (object) {
            // object.quaternion.multiplyQuaternions(
            //     quaternionX,
            //     object.quaternion,
            // );
            // object.quaternion.normalize();
            rotationQueue = [
                {
                    rot: quaternionX,
                },
                ...rotationQueue,
            ];
            console.log("appliedX");
        }
    }

    function rotateY() {
        console.log("new Y");
        if (object) {
            // object.quaternion.multiplyQuaternions(
            //     quaternionY,
            //     object.quaternion,
            // );
            // object.quaternion.normalize();
            rotationQueue = [
                {
                    rot: quaternionY,
                },
                ...rotationQueue,
            ];
        }
    }

    function rotateZ() {
        if (object) {
            // object.quaternion.multiplyQuaternions(
            //     quaternionZ,
            //     object.quaternion,
            // );
            // object.quaternion.normalize();
            rotationQueue = [
                {
                    rot: quaternionZ,
                },
                ...rotationQueue,
            ];
            console.log("appliedZ");
        }
    }
    let curr_interpolation_param: number;
    useTask((frameTime) => {
        if (curr_rotation_params === undefined) {
            let possible_new_rotation = rotationQueue.pop();
            if (possible_new_rotation !== undefined) {
                curr_rotation_params = {
                    start_q: object.quaternion,
                    end_q: new Quaternion().multiplyQuaternions(
                        possible_new_rotation.rot,
                        object.quaternion,
                    ),
                };
                curr_interpolation_param = 0;
            }
        }

        if (
            curr_rotation_params !== undefined &&
            curr_interpolation_param <= 1
        ) {
            const interpolated_quaternion = new Quaternion()
                .slerpQuaternions(
                    curr_rotation_params.start_q,
                    curr_rotation_params.end_q,
                    curr_interpolation_param,
                )
                .normalize();
            object.quaternion.set(
                interpolated_quaternion.x,
                interpolated_quaternion.y,
                interpolated_quaternion.z,
                interpolated_quaternion.w,
            );
            curr_interpolation_param += 0.01;
        }

        if (curr_interpolation_param > 1) {
            curr_rotation_params = undefined;
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

<T.Mesh position={[5, 0, 0]} bind:ref={x_axis_button} on:click={rotateZ}>
    <T.ConeGeometry args={[0.2, 1, 40]} />
    <T.MeshStandardMaterial roughness={0} color={0x0000ff} metalness={"1"} />
</T.Mesh>

<T.Mesh
    position={[0, 5, 0]}
    rotation={[Math.PI / 2, 0, 0]}
    bind:ref={y_axis_button}
    on:click={rotateX}
>
    <T.ConeGeometry args={[0.2, 1, 40]} />
    <T.MeshStandardMaterial roughness={0} color={0x0000ff} metalness={"1"} />
</T.Mesh>

<T.Mesh
    position={[0, 0, 5]}
    rotation={[Math.PI / 2, 0, -Math.PI / 2]}
    bind:ref={z_axis_button}
    on:click={rotateY}
>
    <T.ConeGeometry args={[0.2, 1, 40]} />
    <T.MeshStandardMaterial roughness={0} color={0x0000ff} metalness={"1"} />
</T.Mesh>
