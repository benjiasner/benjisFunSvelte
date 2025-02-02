<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../../stores";

    export let playlistId: number; // Pass the selected playlist ID as a prop
    export let playlistName: string;

    let playlistFiles = []; // Holds the list of files in the playlist
    let allFiles = []; // Holds the list of all files
    let isLoading = false; // Loading state
    let error = null; // Error state
    let loggedInUserId: number | null = null; // Logged-in user ID

    // Fetch the files in the playlist
    async function fetchPlaylistFiles() {
        isLoading = true;
        error = null;
        try {
            const response = await fetch(`https://benjisfunbackend.onrender.com/api/playlists/${playlistId}/files/`);
            if (!response.ok) {
                throw new Error("Failed to fetch playlist files");
            }
            const data = await response.json();
            playlistFiles = data;
        } catch (err) {
            error = err.message;
        } finally {
            isLoading = false;
        }
    }

    // Fetch all files from the API
    async function fetchAllFiles() {
        isLoading = true;
        error = null;
        try {
            const response = await fetch(`https://benjisfunbackend.onrender.com/api/3d-files/`);
            if (!response.ok) {
                throw new Error("Failed to fetch all files");
            }
            const data = await response.json();
            allFiles = data;
        } catch (err) {
            error = err.message;
        } finally {
            isLoading = false;
        }
    }

    // Add a file to the playlist
    async function addFileToPlaylist(fileId: number) {
        try {
            const response = await fetch(`https://benjisfunbackend.onrender.com/api/playlists/${playlistId}/add-file/`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ file_id: fileId }),
            });

            if (!response.ok) {
                throw new Error("Failed to add file to playlist");
            }

            // Refresh the playlist files
            await fetchPlaylistFiles();
        } catch (err) {
            console.error("Error adding file to playlist:", err);
        }
    }

    // Function to handle file selection
    function selectFile(file) {
        selectedFileEntityId.set(file.entity); // Update the store with the selected file's entity ID
    }

    // Fetch playlist files and all files when the component mounts
    onMount(async () => {
        console.log('we iinnit');
        await fetchPlaylistFiles();
        await fetchAllFiles();

        // Fetch the logged-in user's ID
        const endpoint = 'https://benjisfunbackend.onrender.com/api/user/';
        const response = await fetch(endpoint, {
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });

        const content = await response.json();
        loggedInUserId = content.id;
        console.log(loggedInUserId);
    });
</script>

<div class="playlist-view">
    <h2>{playlistName}</h2>

    <!-- Loading State -->
    {#if isLoading}
        <div class="loading">Loading...</div>
    {:else if error}
        <div class="error">{error}</div>
    {:else}
        <!-- Playlist Files -->
        <div class="file-list">
            {#each playlistFiles as file}
                <div class="file-item" on:click={() => selectFile(file)}>
                    <div class="file-name">{file.three_d_file_name}</div>
                    <div class="file-description">{file.three_d_file_description}</div>
                </div>
            {/each}
        </div>

        <!-- All Files -->
        <h4>Add to playlist</h4>
        <div class="file-list">
            {#each allFiles as file}
                <div class="file-item" on:click={() => selectFile(file)}>
                    <div class="file-name">{file.three_d_file_name}</div>
                    <div class="file-description">{file.three_d_file_description}</div>
                    <button on:click|preventDefault={() => addFileToPlaylist(file.id)}>+</button>
                </div>
            {/each}
        </div>
    {/if}
</div>

<style>
    .playlist-view {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        font-family: Arial, sans-serif;
    }

    .file-list {
        max-height: 120px;
        overflow-y: auto;
        border: 1px solid #ccc;
        border-radius: 4px;
        padding: 10px;
        margin-bottom: 20px;
    }

    .file-item {
        padding: 10px;
        border-bottom: 1px solid #eee;
        cursor: pointer;
        display: flex;
        justify-content: space-between;
        align-items: center;
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

    button {
        background-color: #007bff;
        color: white;
        border: none;
        padding: 5px 10px;
        border-radius: 4px;
        cursor: pointer;
    }

    button:hover {
        background-color: #0056b3;
    }
</style>