<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../../stores";
    import ThreeDFileInfo from "../ThreeDFileInfo.svelte";

    let searchQuery: string = ""; // Holds the search input value
    let likedFiles: any[] = []; // Holds the list of liked 3D files
    let isLoading: boolean = false; // Loading state
    let error: string | null = null; // Error state
    let selectedFile: any = null;
    let loggedInUserId: number | null = null; // Logged-in user ID

    let showPopup: boolean = false;
    let popupFile: any = null;

    let liked = true;

    // Function to open the pop-up
    function openPopup(file: any) {
        popupFile = file;
        showPopup = true;
        console.log(file);
    }

    // Function to close the pop-up
    function closePopup() {
        showPopup = false;
        popupFile = null;
    }

    // Function to fetch file data by entity ID
    async function fetchFileData(entityId: number): Promise<any> {
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/entities/${entityId}/`);
            if (!response.ok) {
                throw new Error(`Failed to fetch file data for entity ID ${entityId}`);
            }
            return await response.json();
        } catch (err: any) {
            console.error(`Error fetching file data for entity ID ${entityId}:`, err.message);
            return null;
        }
    }

    // Function to fetch the user's liked files
    async function fetchLikedFiles() {
        isLoading = true;
        error = null;
        try {
            // Fetch the user's liked entities
            const response = await fetch(`http://127.0.0.1:8000/api/user-liked-entities/${loggedInUserId}/`);
            if (!response.ok) {
                throw new Error("Failed to fetch liked entities");
            }
            const likedEntities = await response.json();
            console.log("Liked Entities:", likedEntities);

            // Extract entity IDs from the liked entities
            const entityIds = likedEntities.map((like: any) => like.entity);

            // Fetch file data for each entity ID
            const fileDataPromises = entityIds.map((entityId: number) => fetchFileData(entityId));
            const fileData = await Promise.all(fileDataPromises);

            // Filter out any null values (failed fetches)
            likedFiles = fileData.filter((file) => file !== null);

            // Filter liked files based on the search query
            if (searchQuery) {
                likedFiles = likedFiles.filter((file: any) =>
                    file.three_d_file_name.toLowerCase().includes(searchQuery.toLowerCase())
                );
            }
        } catch (err: any) {
            error = err.message;
        } finally {
            isLoading = false;
        }
    }

    // Function to toggle like/unlike a file
    async function toggleLike(file: any) {
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/toggle-like/${file.entity_id}/`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                credentials: 'include',
            });

            if (!response.ok) {
                throw new Error("Failed to toggle like");
            }

            // Update the liked status of the file
            liked = !liked;
            likedFiles = [...likedFiles]; // Trigger reactivity
        } catch (err: any) {
            console.error("Error toggling like:", err.message);
        }
    }

    // Fetch the logged-in user's ID and liked files when the component mounts
    onMount(async () => {
        // Fetch the logged-in user's ID
        const userResponse = await fetch('http://localhost:8000/api/user/', {
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });
        const userData = await userResponse.json();
        loggedInUserId = userData.id;

        // Fetch the user's liked files
        if (loggedInUserId) {
            await fetchLikedFiles();
        }
    });

    // Debounce the search query to reduce API calls
    let debounceTimeout: number;
    $: if (searchQuery !== undefined && loggedInUserId !== null) {
        clearTimeout(debounceTimeout);
        debounceTimeout = setTimeout(() => {
            fetchLikedFiles();
        }, 300); // 300ms debounce
    }

    // Function to handle file selection
    function selectFile(file: any) {
        selectedFile = file;
        selectedFileEntityId.set(file.entity);
        console.log(file);
        console.log($selectedFileEntityId);
    }
</script>

<div class="container">
    <h2>Liked Files</h2>

    <!-- Search Input -->
    <input
      type="text"
      bind:value={searchQuery}
      placeholder="Search liked files..."
      class="search-box"
    />

    <!-- Loading State -->
    {#if isLoading}
      <div class="loading">Loading...</div>
    {:else if error}
      <div class="error">{error}</div>
    {:else if likedFiles.length === 0}
      <div class="empty-state">No liked files found.</div>
    {:else}
      <!-- Liked Files List -->
      <div class="file-list">
        {#each likedFiles as file}
          <div 
            class="file-item {selectedFile === file ? 'selected': ''}"
            on:click={() => selectFile(file)}
          >
            <div class="file-name">{file.three_d_file_name}</div>
            <div class="file-description">{file.three_d_file_description}</div>
            <div>Added by: {file.username_added}</div>
            <div>Filename: {file.filename}</div>
            <!-- Heart Icon -->
            <div class="heart-icon" on:click|stopPropagation={() => toggleLike(file)}>
                {#if liked}
                    ❤️ <!-- Red heart for liked -->
                {:else}
                    🤍 <!-- Gray heart for not liked -->
                {/if}
            </div>
            <div class="more-info" on:click|stopPropagation={() => openPopup(file)}>
                ...
            </div>  
          </div>
        {/each}
      </div>
    {/if}

    <!-- Pop-up -->
    {#if showPopup && popupFile}
        <ThreeDFileInfo file={popupFile} closePopup={closePopup} />
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

    .file-list {
      max-height: 250px;
      overflow-y: auto;
      border: 1px solid #ccc;
      border-radius: 4px;
      padding: 10px;
    }

    .file-item {
      padding: 10px;
      border-bottom: 1px solid #eee;
      cursor: pointer;
      position: relative;
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

    .empty-state {
      text-align: center;
      color: #888;
    }

    .file-item.selected {
        background-color: #f0f0f0;
        border-left: 5px solid #007bff;
    }

    .heart-icon {
      position: absolute;
      top: 10px;
      right: 35px;
      cursor: pointer;
      font-size: 20px;
    }

    .more-info {
      position: absolute;
      top: 10px;
      right: 10px;
      cursor: pointer;
      font-size: 20px;
    }
</style>