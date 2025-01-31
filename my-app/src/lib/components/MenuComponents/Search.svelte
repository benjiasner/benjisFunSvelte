<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../stores";
  
    let searchQuery = ""; // Holds the search input value
    let files = []; // Holds the list of 3D files
    let isLoading = false; // Loading state
    let error = null; // Error state
    let selectedFile = null;
  
    // Function to fetch 3D files from the API
    async function fetchFiles() {
        isLoading = true;
        error = null;

        try {
            const response = await fetch(`http://127.0.0.1:8000/api/3d-files/?search=${searchQuery}`);
            if (!response.ok) {
                throw new Error("Failed to fetch 3D files");
            }
            files = await response.json();
        } catch (err) {
            error = err.message;
        } finally {
            isLoading = false;
        }
    }
  
    // Fetch files when the component mounts
    onMount(() => {
      fetchFiles();
    });
  
    // Fetch files whenever the search query changes
    $: if (searchQuery !== undefined) {
        fetchFiles();
    }

    function selectFile(file) {
        selectedFile = file;
        selectedFileEntityId.set(file.entity);
    }
  </script>
  
  <div class="container">
    <h1>3D Files</h1>
  
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
          </div>
        {/each}
      </div>
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
      max-height: 300px;
      overflow-y: auto;
      border: 1px solid #ccc;
      border-radius: 4px;
      padding: 10px;
    }
  
    .file-item {
      padding: 10px;
      border-bottom: 1px solid #eee;
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
  </style>