<script>

    import { onMount } from 'svelte';

    let username = ''

    const logout = async () => {
        const endpoint = 'http://localhost:8000/api/logout/';
        const response = await fetch(endpoint, {
            method: "POST",
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });

        const content = await response.json();
        console.log(content);
        username = content.username;
    }

    onMount(async () => {
        const endpoint = 'http://localhost:8000/api/user/';
        const response = await fetch(endpoint, {
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });

        const content = await response.json();
        console.log(content);
        username = content.username;
    });

</script>

{#if username}
    <h1>Welcome to your svelte tester and development site, {username}!</h1>
{:else}
    <h1>Welcome to your svelte tester and development site.</h1>
{/if}
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

<h2>Site Functions</h2>
<ul>
    <li>
        <div>
            <a href="/signin">Sign in</a>
        </div>
    </li>
    <li>
        <div>
            <a href="/register">Sign up</a>
        </div>
    </li>
    <li>
        <div>
            <a href="#" on:click={logout}>Log out</a>
        </div>
    </li>
</ul>

<h2>Threlte Projects</h2>
<ul>
    <li>
        <div>
            <a href="/gettingStarted">Getting Started in Threlte</a>
        </div>
    </li>
    <li>
        <div>
            <a href="/importingSTL">Importing and Viewing an STL in Threlte</a>
        </div>
    </li>
    <li>
        <div>
            <a href="/transformControls">Using Transform Controls</a>
        </div>
    </li>
    <li>
        <div>
            <a href="/refactor">Refactor button reassignment</a>
        </div>
    </li>
    <li>
        <div>
            <a href="/quaternions">Quaternions</a>
        </div>
    </li>
    <li>
        <div>
            <a href="/menu1">Menu</a>
        </div>
    </li>
</ul>
<h2>Playing with Forms Projects</h2>
<ul>
    <li>
        <div>
            <a href="/forms1">Basic Form Submitting</a>
        </div>
    </li>
</ul>