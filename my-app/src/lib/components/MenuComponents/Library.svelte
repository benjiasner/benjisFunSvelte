<script lang="ts">
    import { onMount } from "svelte";
    import { selectedFileEntityId } from "../../stores";
  
    let searchQuery = ""; // Holds the search input value
    let lists = []; // Holds the list of lists (e.g., "Likes")
    let isLoading = false; // Loading state
    let error = null; // Error state
    let selectedFile = null;
    let loggedInUserId: number | null = null; // Logged-in user ID
    let expandedList: string | null = null; // Tracks which list is expanded

</script>
  
<div class="container">
    <h2>Library</h2>
  
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
        <div class="file-item">Likes</div>
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