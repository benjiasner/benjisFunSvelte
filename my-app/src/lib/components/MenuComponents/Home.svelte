<script>
    import { onMount } from 'svelte';

    let username = ''
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

<h1>Welcome, {username}</h1>