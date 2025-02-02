<script lang="ts">
    import { onMount } from "svelte";

    let user_id;
  
    // Variables to hold form data
    let title = "";
    let error: string | null = null;
    let isSubmitting = false;

    async function createPlaylist(playlistName) {

        const response = await fetch('https://benjisfunbackend.onrender.com/api/create-playlist/', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({
                name: playlistName,
                user: user_id,
            }),
        });

        const data = await response.json();
        console.log('Playlist created:', data);
    }

  
    async function handleSubmit(event: Event) {
        event.preventDefault();

        // Basic validation
        if (title.trim() === "") {
            error = "Title is required";
            return;
        }

        error = null;
        isSubmitting = true;

        try {
            // Send the title to the backend
            console.log("Creating playlist with title:", title);
            await createPlaylist(title);

            // Reset the form
            title = "";
        } catch (err) {
            console.error('Error creating playlist:', err);
        } finally {
            isSubmitting = false;
        }
    }

    onMount(async () => {
        const endpoint = 'https://benjisfunbackend.onrender.com/api/user/';
        const response = await fetch(endpoint, {
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
        });

        const content = await response.json();
        console.log(content);
        user_id = content.id;
        console.log(user_id)
    });
  </script>
  
  <!-- Form Layout -->
  <div class="playlist-form-container">
    <h2>Create a New Playlist</h2>
  
    <form on:submit={handleSubmit} class="playlist-form">
      <!-- Playlist Title Input -->
      <label for="title">Playlist Title</label>
      <input
        type="text"
        id="title"
        bind:value={title}
        placeholder="Enter playlist title"
        class="title-input"
      />
  
      <!-- Display error if any -->
      {#if error}
        <div class="error-message">{error}</div>
      {/if}
  
      <!-- Submit Button -->
      <button type="submit" disabled={isSubmitting} class="submit-button">
        {isSubmitting ? "Creating..." : "Create Playlist"}
      </button>
    </form>
  </div>
  
  <style>
    .playlist-form-container {
      max-width: 400px;
      margin: 0 auto;
      padding: 20px;
      background-color: black;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
  
    h2 {
      text-align: center;
      margin-bottom: 20px;
    }
  
    .playlist-form {
      display: flex;
      flex-direction: column;
    }
  
    label {
      margin-bottom: 8px;
      font-weight: bold;
    }
  
    .title-input {
      padding: 10px;
      border: 1px solid #ced4da;
      border-radius: 4px;
      margin-bottom: 12px;
    }
  
    .error-message {
      color: #dc3545;
      margin-bottom: 12px;
    }
  
    .submit-button {
      background-color: #007bff;
      color: white;
      padding: 10px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }
  
    .submit-button:disabled {
      background-color: #6c757d;
      cursor: not-allowed;
    }
  
    .submit-button:hover:not(:disabled) {
      background-color: #0056b3;
    }
  </style>
  