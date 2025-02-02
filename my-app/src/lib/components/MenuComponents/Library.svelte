<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../stores";
    import LikesList from "./MenuSubComponents/LikesList.svelte"
    import CreatePlaylist from "./MenuSubComponents/CreatePlaylist.svelte"
    import PlaylistView from "./MenuSubComponents/PlaylistView.svelte"
  
    let searchQuery = ""; // Holds the search input value
    let lists = []; // Holds the list of playlists
    let filteredLists = []; // Holds the filtered list of playlists
    let isLoading = false; // Loading state
    let error = null; // Error state
    let selectedFile = null;
    let loggedInUserId: number | null = null; // Logged-in user ID
    let expandedList: string | null = null; // Tracks which list is expanded
    let view = 'main';
    let selectedPlaylistId: number | null = null;
    let selectedPlaylistName: string | null = null;

    function likesListChosen() {
        view = 'likes'
        console.log("yeet")
    }

    function createPlaylistChosen() {
        view = 'create'
        console.log("yeet2")
    }

    function playlistChosen(playlistId: number, playlistName: string) {
        console.log("Playlist chosen:", playlistId); // Debugging
        view = 'playlist';
        selectedPlaylistId = playlistId;
        selectedPlaylistName = playlistName;
        console.log("View:", view); // Debugging
        console.log("Selected Playlist ID:", selectedPlaylistId); // Debugging
    }

    // Fetch playlists for the logged-in user
    async function fetchPlaylists(userId: number) {
        isLoading = true;
        error = null;
        try {
            const response = await fetch(`https://benjisfunbackend.onrender.com/api/user-playlists/${userId}/?search=${searchQuery}`);
            if (!response.ok) {
                throw new Error("Failed to fetch playlists");
            }
            const data = await response.json();
            lists = data; // Update the lists array with the fetched playlists
            filterPlaylists(); // Filter playlists based on the search query
            console.log(lists)
        } catch (err) {
            error = err.message;
        } finally {
            isLoading = false;
        }
    }

    // Filter playlists based on the search query
    function filterPlaylists() {
        if (searchQuery) {
            filteredLists = lists.filter((playlist) =>
                playlist.name.toLowerCase().includes(searchQuery.toLowerCase())
            );
        } else {
            filteredLists = lists; // Show all playlists if no search query
        }
    }

    // Fetch playlists when the search query changes
    $: if (searchQuery !== undefined && loggedInUserId !== null) {
        fetchPlaylists(loggedInUserId);
    }

    onMount(async () => {
        // Fetch the logged-in user's ID
        const endpoint = 'https://benjisfunbackend.onrender.com/api/user/';
        const response = await fetch(endpoint, {
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });

        const content = await response.json();
        console.log(content);
        loggedInUserId = content.id;
        console.log(loggedInUserId);

        // Fetch playlists for the logged-in user
        if (loggedInUserId) {
            await fetchPlaylists(loggedInUserId);
        }
    });
</script>
  
<div class="container">
    {#if view === 'main'}
        <div class="first-bar">
            <h2>Library</h2>
            <h1 class="add-button" on:click|preventDefault={createPlaylistChosen}>+</h1>
        </div>
    
        <!-- Search Input -->
        <input
        type="text"
        bind:value={searchQuery}
        placeholder="Search your library..."
        class="search-box"
        />
    
        <!-- Loading State -->
        {#if isLoading}
        <div class="loading">Loading...</div>
        {:else if error}
        <div class="error">{error}</div>
        {:else}
        <!-- Lists -->
        <div class="file-list">
            <div class="file-item" on:click|preventDefault={likesListChosen}>Likes</div>
            {#each filteredLists as playlist}
                <div class="file-item" on:click|preventDefault={() => playlistChosen(playlist.entity, playlist.name)}>
                    <div class="file-name">{playlist.name}</div>
                </div>
            {/each}
        </div>
        {/if}
    {:else if view === 'likes'}
        <LikesList />
    {:else if view === 'create'}
        <CreatePlaylist />
    {:else if view === 'playlist' && selectedPlaylistId}
        <PlaylistView playlistId={selectedPlaylistId} playlistName={selectedPlaylistName}/>
    {/if}
</div>

<style>
    .container {
      max-width: 600px;
      margin: 0 auto;
      padding: 20px;
      font-family: Arial, sans-serif;
    }
  
    .search-box {
      width: 100%;
      padding: 10px;
      margin-bottom: 20px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
  
    .lists {
      max-height: 350px;
      overflow-y: auto;
      border: 1px solid #ccc;
      border-radius: 4px;
      padding: 10px;
    }

    .list-item {
      margin-bottom: 10px;
    }

    .list-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
      background-color: #f0f0f0;
      border-radius: 4px;
      cursor: pointer;
    }

    .list-header:hover {
      background-color: #e0e0e0;
    }

    .toggle-icon {
      font-size: 14px;
    }

    .file-list {
        max-height: 350px;
        overflow-y: auto;
        border: 1px solid #ccc;
        border-radius: 4px;
        padding: 10px;
    }
  
    .file-item {
        padding: 10px;
        border-bottom: 1px solid #eee;
        cursor: pointer;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .file-item:hover {
        background-color: #f0f0f0;
    }

    .file-item:last-child {
      border-bottom: none;
    }
  
    .file-name {
      font-weight: bold;
    }
  
    .file-description {
      color: #666;
      font-size: 14px;
    }
  
    .loading {
      text-align: center;
      color: #888;
    }
  
    .error {
      color: red;
      text-align: center;
    }

    /* Highlight selected file */
    .file-item.selected {
        background-color: #f0f0f0;
        border-left: 5px solid #007bff;
    }

    .first-bar {
        display: flex; /* Use Flexbox to align items */
        justify-content: space-between; /* Space out the items */
        align-items: center; /* Vertically center the items */
        padding: 10px; /* Add some padding */
        border-bottom: 1px solid #ccc; /* Optional: Add a border for separation */
    }

    .add-button {
        cursor: pointer; /* Make the + button look clickable */
        margin-left: auto; /* Push the + button to the right */
        font-size: 24px; /* Increase the size of the + button */
        color: #007bff; /* Change the color to blue (or any color you prefer) */
    }

    .add-button:hover {
        color: #0056b3; /* Change color on hover for interactivity */
    }
</style>