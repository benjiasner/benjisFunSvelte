<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../../stores";

    export let playlistId: number; // Pass the selected playlist ID as a prop
    export let playlistName: string;

    let playlistFiles = []; // Holds the list of files in the playlist
    let isLoading = false; // Loading state
    let error = null; // Error state
    let selectedFile = null;

    // Fetch the files in the playlist
    async function fetchPlaylistFiles() {
        isLoading = true;
        error = null;
        try {
            const response = await fetch(`http://localhost:8000/api/playlists/${playlistId}/files/`);
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

    // Add a file to the playlist
    async function addFileToPlaylist(fileId: number) {
        try {
            const response = await fetch(`http://localhost:8000/api/playlists/${playlistId}/add-file/`, {
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

    // Fetch playlist files when the component mounts
    onMount(async () => {
        console.log('we iinnit')
        await fetchPlaylistFiles();
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
        <!-- Add File Section -->
        <div class="add-file-section">
            <input
                type="text"
                placeholder="Search files to add..."
                class="search-box"
            />
            <!-- Display search results here -->
        </div>

        <!-- Playlist Files -->
        <div class="file-list">
            {#each playlistFiles as file}
                <div class="file-item">
                    <div class="file-name">{file.three_d_file_name}</div>
                    <div class="file-description">{file.three_d_file_description}</div>
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

    .add-file-section {
        margin-bottom: 20px;
    }

    .search-box {
        width: 100%;
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
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
</style>