<script lang="ts">
    let username = '';
    let password = '';
    let confirmPassword = '';
    let email = '';
    let errorMessage = '';
    let successMessage = '';

    let signIn = () => {
        const endpoint = 'https://benjisfunbackend.onrender.com/api/login/';
        const requestOptions = {
            method: "POST",
            headers: {'Content-Type': 'application/json'},
            credentials: 'include',
            body: JSON.stringify({ username: username, password: password })
        }

        fetch(endpoint, requestOptions)
            .then(response => response.json())
            .then(data => {
              localStorage.setItem('jwt', data.jwt);
            })
      }


  </script>
  
  <div class="signup-container">
    <h2>Sign in</h2>
  
    <!-- Error Message -->
    {#if errorMessage}
      <div class="error">{errorMessage}</div>
    {/if}
  
    <!-- Success Message -->
    {#if successMessage}
      <div class="success">{successMessage}</div>
    {/if}
  
    <!-- Sign Up Form -->
    <form on:submit|preventDefault={signIn}>
      <label for="username">Username</label>
      <input
        type="text"
        id="username"
        bind:value={username}
        placeholder="Enter your username"
      />
  
      <label for="password">Password</label>
      <input
        type="password"
        id="password"
        bind:value={password}
        placeholder="Enter your password"
      />
  
      <button type="submit">Sign In</button>
    </form>
  </div>


  <style>
    .signup-container {
      max-width: 400px;
      margin: 0 auto;
      padding: 20px;
      border: 1px solid #ccc;
      border-radius: 8px;
      background-color: #f9f9f9;
    }
  
    h2 {
      text-align: center;
      margin-bottom: 20px;
    }
  
    label {
      display: block;
      margin-bottom: 8px;
      font-weight: bold;
    }
  
    input {
      width: 100%;
      padding: 8px;
      margin-bottom: 16px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
  
    button {
      width: 100%;
      padding: 10px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
  
    button:hover {
      background-color: #0056b3;
    }
  
    .error {
      color: red;
      margin-bottom: 16px;
    }
  
    .success {
      color: green;
      margin-bottom: 16px;
    }
  </style>
  