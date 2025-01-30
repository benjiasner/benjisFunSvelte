<script lang="ts">
    let username = '';
    let password = '';
    let confirmPassword = '';
    let email = '';
    let errorMessage = '';
    let successMessage = '';

    let signUp = () => {
        const endpoint = 'http://localhost:8000/api/register/';
        const requestOptions = {
            method: "POST",
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({ username: username, password: password })
        }

        fetch(endpoint, requestOptions)
            .then(response => response.json())
            .then(data => {
                console.log(data)
            })
        }


  </script>
  
  <div class="signup-container">
    <h2>Sign Up</h2>
  
    <!-- Error Message -->
    {#if errorMessage}
      <div class="error">{errorMessage}</div>
    {/if}
  
    <!-- Success Message -->
    {#if successMessage}
      <div class="success">{successMessage}</div>
    {/if}
  
    <!-- Sign Up Form -->
    <form on:submit|preventDefault={signUp}>
      <label for="username">Username</label>
      <input
        type="text"
        id="username"
        bind:value={username}
        placeholder="Enter your username"
      />
  
      <label for="email">Email</label>
      <input
        type="email"
        id="email"
        bind:value={email}
        placeholder="Enter your email"
      />
  
      <label for="password">Password</label>
      <input
        type="password"
        id="password"
        bind:value={password}
        placeholder="Enter your password"
      />
  
      <label for="confirmPassword">Confirm Password</label>
      <input
        type="password"
        id="confirmPassword"
        bind:value={confirmPassword}
        placeholder="Confirm your password"
      />
  
      <button type="submit">Sign Up</button>
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
  