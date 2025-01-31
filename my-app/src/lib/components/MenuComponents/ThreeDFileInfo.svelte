<script lang="ts">
    import { onMount } from "svelte";

    export let file: any; // The file data passed from the parent component
    export let closePopup: () => void; // Function to close the pop-up

    let totalLikes: number | null = null; // Total likes for the file
    let isLoadingLikes = false; // Loading state for likes
    let errorLikes: string | null = null; // Error state for likes

    let totalStreams: number | null = null; // Total streams for the file
    let isLoadingStreams = false; // Loading state for streams
    let errorStreams: string | null = null; // Error state for streams

    let totalDownloads: number | null = null; // Total downloads for the file
    let isLoadingDownloads = false; // Loading state for downloads
    let errorDownloads: string | null = null; // Error state for downloads

    // Function to fetch total downloads for the file
    async function fetchTotalDownloads(fileId: number) {
        isLoadingDownloads = true;
        errorDownloads = null;
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/total-downloads/${fileId}/`);
            if (!response.ok) {
                throw new Error("Failed to fetch total downloads");
            }
            const data = await response.json();
            totalDownloads = data.total_downloads;
        } catch (err) {
            console.error("Error fetching total downloads:", err);
            errorDownloads = err.message;
        } finally {
            isLoadingDownloads = false;
        }
    }

    // Function to handle file download
    async function downloadFile(fileId: number, filename: string) {
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/download-file/${fileId}/`);
            if (!response.ok) {
                throw new Error("Failed to download file");
            }

            // Convert the response to a Blob
            const blob = await response.blob();

            // Create a temporary link element to trigger the download
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.href = url;
            a.download = filename; // Set the filename for the download
            document.body.appendChild(a);
            a.click(); // Trigger the download
            document.body.removeChild(a); // Clean up
            window.URL.revokeObjectURL(url); // Release the object URL
        } catch (err) {
            console.error("Error downloading file:", err);
            alert("Failed to download file. Please try again.");
        }
    }

    // Fetch total likes and streams when the component mounts
    onMount(async () => {
        if (file?.entity) {
            await fetchTotalLikes(file.entity);
        }
        if (file?.id) {
            await fetchTotalStreams(file.id);
            await fetchTotalDownloads(file.id);
        }
    });

    // Function to fetch total likes for the entity
    async function fetchTotalLikes(entityId: number) {
        isLoadingLikes = true;
        errorLikes = null;
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/total-likes/${entityId}/`);
            if (!response.ok) {
                throw new Error("Failed to fetch total likes");
            }
            const data = await response.json();
            totalLikes = data.total_likes;
        } catch (err) {
            console.error("Error fetching total likes:", err);
            errorLikes = err.message;
        } finally {
            isLoadingLikes = false;
        }
    }

    // Function to fetch total streams for the file
    async function fetchTotalStreams(fileId: number) {
        isLoadingStreams = true;
        errorStreams = null;
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/total-streams/${fileId}/`);
            if (!response.ok) {
                throw new Error("Failed to fetch total streams");
            }
            const data = await response.json();
            totalStreams = data.total_streams;
        } catch (err) {
            console.error("Error fetching total streams:", err);
            errorStreams = err.message;
        } finally {
            isLoadingStreams = false;
        }
    }
</script>

<!-- Pop-up overlay -->
<div class="popup-overlay" on:click|self={closePopup}>
    <div class="popup-content">
        {#if file}
            <h2>{file.three_d_file_name}</h2>
            <p>{file.three_d_file_description}</p>
            <p><strong>Added by:</strong> {file.username_added}</p>
            <p><strong>Description:</strong> {file.description}</p>
            <p><strong>Filename:</strong> {file.filename}</p>
            <p><strong>Likes:</strong>
                {#if isLoadingLikes}
                    <span>Loading...</span>
                {:else if errorLikes}
                    <span class="error">Error: {errorLikes}</span>
                {:else}
                    <span>{totalLikes}</span>
                {/if}
            </p>
            <p><strong>Streams:</strong>
                {#if isLoadingStreams}
                    <span>Loading...</span>
                {:else if errorStreams}
                    <span class="error">Error: {errorStreams}</span>
                {:else}
                    <span>{totalStreams}</span>
                {/if}
            </p>
            <p><strong>Downloads:</strong>
                {#if isLoadingDownloads}
                    <span>Loading...</span>
                {:else if errorDownloads}
                    <span class="error">Error: {errorDownloads}</span>
                {:else}
                    <span>{totalDownloads}</span>
                {/if}
            </p>
            <p><strong>Download:</strong>
                <button on:click|stopPropagation={() => downloadFile(file.id, file.filename)}>
                    Download File
                </button>
            </p>
        {:else}
            <p>No file data available.</p>
        {/if}
        <button on:click={closePopup}>Close</button>
    </div>
</div>

<style>
    .popup-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.5);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    .popup-content {
        background: black;
        padding: 20px;
        border-radius: 8px 8px 0 0;
        max-width: 100%;
        width: 350px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    h2 {
        margin-top: 0;
    }

    button {
        margin-top: 10px;
        padding: 8px 16px;
        background: #007bff;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }

    button:hover {
        background: #0056b3;
    }

    .error {
        color: red;
    }
</style>