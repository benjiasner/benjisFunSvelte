<script>
    import { T, useFrame } from "@threlte/core";
    import { OrbitControls, Sky, interactivity } from "@threlte/extras";
    import * as THREE from "three";

    interactivity();

    let torusMesh;
    let orientationViewToggle = false;
    let clickCooldown = false;
    let colorsArrows = ["gray", "gray", "gray"];

    const quaternionX = new THREE.Quaternion().setFromAxisAngle(
        new THREE.Vector3(1, 0, 0),
        Math.PI / 2,
    );
    const quaternionY = new THREE.Quaternion().setFromAxisAngle(
        new THREE.Vector3(0, 1, 0),
        Math.PI / 2,
    );
    const quaternionZ = new THREE.Quaternion().setFromAxisAngle(
        new THREE.Vector3(0, 0, 1),
        Math.PI / 2,
    );

    function toggleOrientationView() {
        orientationViewToggle = !orientationViewToggle;
        console.log("clicking to see if it clicks");
    }

    function rotateX() {
        if (torusMesh) {
            torusMesh.quaternion.multiplyQuaternions(
                quaternionX,
                torusMesh.quaternion,
            );
            //torusMesh.quaternion.normalize();
            // torusMesh.quaternion.multiplyQuaternions(quaternionX, torusMesh.quaternion);
            torusMesh.quaternion.normalize();
            console.log("appliedX");
        }
    }

    function rotateY() {
        if (torusMesh) {
            torusMesh.quaternion.multiplyQuaternions(
                quaternionY,
                torusMesh.quaternion,
            );
            // torusMesh.quaternion.normalize();
            // torusMesh.quaternion.multiplyQuaternions(quaternionY, torusMesh.quaternion);
            torusMesh.quaternion.normalize();
            console.log("appliedY");
        }
    }

    function rotateZ() {
        if (torusMesh) {
            torusMesh.quaternion.multiplyQuaternions(
                quaternionZ,
                torusMesh.quaternion,
            );
            // torusMesh.quaternion.normalize();
            // torusMesh.quaternion.multiplyQuaternions(quaternionZ, torusMesh.quaternion);
            torusMesh.quaternion.normalize();
            console.log("appliedZ");
        }
    }

    function hoverOnArrow(arrowIndex) {
        colorsArrows[arrowIndex] = "blue";
        console.log("hovered on arrow");
    }

    function hoverOffArrow(arrowIndex) {
        colorsArrows[arrowIndex] = "gray";
        console.log("hovered off arrow");
    }

    useFrame(() => {
        const c = "c";
    });
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]} zoom={2} fov={80}>
    <OrbitControls autoRotate enableDamping autoRotateSpeed={0} />
</T.PerspectiveCamera>

<Sky />

<T.Group>
    <T.Mesh
        bind:ref={torusMesh}
        position={[0, 0, 0]}
        on:click={toggleOrientationView}
    >
        <T.ConeGeometry />
        <T.MeshStandardMaterial roughness={0} color="gray" metalness={1} />
    </T.Mesh>
</T.Group>

{#if orientationViewToggle}
    <T.Mesh
        position={[0, 5, 0]}
        rotation={[Math.PI / 2, 0, 0]}
        on:click={rotateX}
        on:pointerover={() => hoverOnArrow(0)}
        on:pointerout={() => hoverOffArrow(0)}
    >
        <T.ConeGeometry args={[0.2, 1, 40]} />
        <T.MeshStandardMaterial
            roughness={0}
            color={colorsArrows[0]}
            metalness={1}
        />
    </T.Mesh>

    <T.Mesh
        position={[0, 0, 5]}
        rotation={[Math.PI / 2, 0, -Math.PI / 2]}
        on:click={rotateY}
        on:pointerover={() => hoverOnArrow(1)}
        on:pointerout={() => hoverOffArrow(1)}
    >
        <T.ConeGeometry args={[0.2, 1, 40]} />
        <T.MeshStandardMaterial
            roughness={0}
            color={colorsArrows[1]}
            metalness={1}
        />
    </T.Mesh>

    <T.Mesh
        position={[5, 0, 0]}
        rotation={[Math.PI, 0, Math.PI]}
        on:click={rotateZ}
        on:pointerover={() => hoverOnArrow(2)}
        on:pointerout={() => hoverOffArrow(2)}
    >
        <T.ConeGeometry args={[0.2, 1, 40]} />
        <T.MeshStandardMaterial
            roughness={0}
            color={colorsArrows[2]}
            metalness={1}
        />
    </T.Mesh>
{/if}

<T.GridHelper />
