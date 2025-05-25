<script>
  let nome = $state('');
  let cognome = $state('');
  let username = $state('');
  let email = $state('');
  let password = $state('');
  let isLoading = $state(false);
  let error = $state('');
  let success = $state('');
  import { goto } from '$app/navigation';

  async function handleRegistration(event) {
    event.preventDefault();
    
    error = '';
    success = '';
    
    if (!nome || !cognome || !username || !email || !password) {
      error = 'Tutti i campi sono obbligatori';
      return;
    }
    
    isLoading = true;
    
    try {
      const res = await fetch('https://backendls-1.onrender.com/register', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ 
          nome, 
          cognome, 
          username, 
          email, 
          password 
        }),
        credentials: 'include'
      });
      
      if (!res.ok) {
        const data = await res.json();
        error = data.message || 'Errore durante la registrazione';
        return;
      }
      
      success = 'Registrazione completata con successo!';
      
      setTimeout(() => {
        goto('/');
      }, 2000);
      
    } catch (err) {
      error = 'Errore di rete durante la registrazione';
    } finally {
      isLoading = false;
    }
  }
</script>

<svelte:head>
  <title>Registrazione</title>
</svelte:head>

<main class="login-container">
  <div class="login-card">
    <h1>Registrati</h1>
   
    <form onsubmit={handleRegistration}>
      <div class="field">
        <label for="nome">Nome</label>
        <input
          id="nome"
          type="text"
          bind:value={nome}
          placeholder="Il tuo nome"
          required
        />
      </div>
      
      <div class="field">
        <label for="cognome">Cognome</label>
        <input
          id="cognome"
          type="text"
          bind:value={cognome}
          placeholder="Il tuo cognome"
          required
        />
      </div>
      
      <div class="field">
        <label for="username">Username</label>
        <input
          id="username"
          type="text"
          bind:value={username}
          placeholder="La tua username"
          required
        />
      </div>
      
      <div class="field">
        <label for="email">Email</label>
        <input
          id="email"
          type="email"
          bind:value={email}
          placeholder="La tua email"
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
      
      {#if success}
        <div class="success">{success}</div>
      {/if}
      
      <button type="submit" disabled={isLoading}>
        {isLoading ? 'Registrazione in corso...' : 'Registrati'}
      </button>
    </form>
    
    <p class="forgot-password">
      <a href="/">Hai già un account? Accedi</a>
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
  
  .success {
    color: #155724;
    background-color: #d4edda;
    border: 1px solid #c3e6cb;
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