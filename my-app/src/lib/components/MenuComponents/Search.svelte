<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../stores";
    import ThreeDFileInfo from "./ThreeDFileInfo.svelte"
    
    let searchQuery = ""; // Holds the search input value
    let files = []; // Holds the list of 3D files
    let isLoading = false; // Loading state
    let error = null; // Error state
    let selectedFile = null;
    let loggedInUserId = null; // Replace this with the actual logged-in user ID

    let showPopup = false;
    let popupFile = null;

    // Function to open the pop-up
    function openPopup(file) {
        popupFile = file;
        showPopup = true;
        console.log(file);
    }

    // Function to close the pop-up
    function closePopup() {
        showPopup = false;
        popupFile = null;
    }

    // Function to fetch 3D files from the API
    async function fetchFiles() {
        isLoading = true;
        error = null;
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/3d-files/?search=${searchQuery}`);
            if (!response.ok) throw new Error("Failed to fetch 3D files");
            
            // Create NEW array with fresh objects
            const newFiles = await response.json();
            
            // Add liked status to NEW objects
            await Promise.all(newFiles.map(async (file) => {
                file.liked = await checkLikeStatus(file.entity, loggedInUserId);
            }));
            
            // Replace entire array to trigger reactivity
            files = newFiles;
        } catch (err) {
            error = err.message;
        } finally {
            isLoading = false;
        }
    }

    // Function to check if a file is liked by the logged-in user
    async function checkLikeStatus(entityId: number, userId: number): Promise<boolean> {
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/check-like/${entityId}/${userId}/`);
            if (!response.ok) {
                throw new Error("Failed to fetch like status");
            }
            const data = await response.json();
            return data.is_liked;
        } catch (err) {
            console.error("Error checking like status:", err);
            return false;
        }
    }

    // Function to like an entity
    async function likeEntity(entityId: number, userId: number) {
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/like/${entityId}/${userId}/`, {
                method: 'POST',
            });
            if (!response.ok) {
                throw new Error("Failed to like entity");
            }
            return true;
        } catch (err) {
            console.error("Error liking entity:", err);
            return false;
        }
    }

    // Function to unlike an entity
    async function unlikeEntity(entityId: number, userId: number) {
        try {
            const response = await fetch(`http://127.0.0.1:8000/api/unlike/${entityId}/${userId}/`, {
                method: 'POST',
            });
            if (!response.ok) {
                throw new Error("Failed to unlike entity");
            }
            return true;
        } catch (err) {
            console.error("Error unliking entity:", err);
            return false;
        }
    }

    // Function to handle heart icon click
    async function toggleLike(file) {
        if (file.liked) {
            const success = await unlikeEntity(file.entity, loggedInUserId);
            if (success) {
                file.liked = false;
            }
        } else {
            const success = await likeEntity(file.entity, loggedInUserId);
            if (success) {
                file.liked = true;
            }
        }
        files = files;
    }

    // Fetch files when the component mounts

    onMount(async () => {
        const endpoint = 'http://localhost:8000/api/user/';
        const response = await fetch(endpoint, {
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });

        const content = await response.json();
        // console.log(content);
        loggedInUserId = content.id;
        console.log(loggedInUserId);
        await fetchFiles();
    });

    // Fetch files whenever the search query changes
    $: if (searchQuery !== undefined && loggedInUserId !== null) {
        fetchFiles();
    }

    function selectFile(file) {
        selectedFile = file;
        selectedFileEntityId.set(file.entity);
    }
    function toggleInfo() {
        console.log("clicked")
    }
</script>

<div class="container">
    <!-- Search Input -->
    <input
      type="text"
      bind:value={searchQuery}
      placeholder="Search 3D files..."
      class="search-box"
    />

    <!-- Loading State -->
    {#if isLoading}
      <div class="loading">Loading...</div>
    {:else if error}
      <div class="error">{error}</div>
    {:else}
      <!-- File List -->
      <div class="file-list">
        {#each files as file}
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
                {#if file.liked}
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