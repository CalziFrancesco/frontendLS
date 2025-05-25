<script>
  let username = $state('');
  let password = $state('');
  let isLoading = $state(false);
  let error = $state('');
  import {goto} from '$app/navigation'

  async function handleLogin(event) {
    event.preventDefault();
    
    if (!username || !password) {
        error = 'Inserisci username e password';
        return;
    }

    try {
        const res = await fetch('https://backendls-1.onrender.com/login', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ username, password }),
            credentials: 'include' 
        });

        if (!res.ok) {
            const data = await res.json();
            error = data.message || 'Errore durante il login';
            return;
        }
        if (username=="Admin"){
          goto('/admin')
        }else{
          goto('/home')
        }
    } catch (err) {
        error = 'Errore di rete durante il login';
    }
}

</script>

<main class="login-container">
  <div class="login-card">
    <h1>Accedi</h1>
    
    <form onsubmit={handleLogin}>
      <div class="field">
        <label for="username">Username</label>
        <input 
          id="username"
          type="username" 
          bind:value={username}
          placeholder="La tua username"
          required
        />
      </div>

      <div class="field">
        <label for="password">Password</label>
        <input 
          id="password"
          type="password" 
          bind:value={password}
          placeholder="La tua password"
          required
        />
      </div>

      {#if error}
        <div class="error">{error}</div>
      {/if}

      <button type="submit" disabled={isLoading}>
        {isLoading ? 'Accesso in corso...' : 'Accedi'}
      </button>
    </form>

    <p class="forgot-password">
      <a href="/registrazione">Non hai un account? Registrati.</a>
    </p>
  </div>
</main>

<style>
  .login-container {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #f5f5f5;
    font-family: system-ui, -apple-system, sans-serif;
  }

  .login-card {
    background: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    width: 100%;
    max-width: 400px;
  }

  h1 {
    text-align: center;
    margin-bottom: 2rem;
    color: #333;
  }

  .field {
    margin-bottom: 1rem;
  }

  label {
    display: block;
    margin-bottom: 0.5rem;
    color: #555;
    font-weight: 500;
  }

  input {
    width: 100%;
    padding: 0.75rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 1rem;
    box-sizing: border-box;
  }

  input:focus {
    outline: none;
    border-color: #007bff;
    box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25);
  }

  button {
    width: 100%;
    padding: 0.75rem;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.2s;
  }

  button:hover:not(:disabled) {
    background-color: #0056b3;
  }

  button:disabled {
    background-color: #6c757d;
    cursor: not-allowed;
  }

  .error {
    color: #dc3545;
    background-color: #f8d7da;
    border: 1px solid #f5c6cb;
    padding: 0.75rem;
    border-radius: 4px;
    margin-bottom: 1rem;
    font-size: 0.9rem;
  }

  .forgot-password {
    text-align: center;
    margin-top: 1rem;
  }

  .forgot-password a {
    color: #007bff;
    text-decoration: none;
  }

  .forgot-password a:hover {
    text-decoration: underline;
  }
</style>