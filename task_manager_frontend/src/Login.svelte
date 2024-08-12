<script>
  import { createEventDispatcher } from 'svelte';
  let email = '';
  let password = '';
  let errorMessage = '';
  const dispatch = createEventDispatcher();

  async function handleSubmit(event) {
    event.preventDefault();

    try {
      const response = await fetch('http://localhost:8055/auth/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ email, password })
      });

      if (response.ok) {
        const data = await response.json();
        localStorage.setItem('token', data.data.access_token);
        localStorage.setItem('loggedIn', 'true');
        dispatch('login');
      } else {
        const errorData = await response.json();
        errorMessage = errorData.errors[0].message || 'Login failed';
      }
    } catch (error) {
      errorMessage = 'An error occurred. Please try again.';
    }
  }
</script>

<div class="flex justify-center items-center min-h-screen bg-gray-100">
  <div class="w-full max-w-sm bg-white p-6 rounded-lg shadow-lg">
    <h2 class="text-2xl font-bold text-blue-600 mb-6">Login</h2>
    <form on:submit={handleSubmit}>
      <div class="mb-4">
        <label for="email" class="block text-gray-700 font-bold mb-2">Email:</label>
        <input id="email" type="email" bind:value={email} required class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
      </div>

      <div class="mb-4">
        <label for="password" class="block text-gray-700 font-bold mb-2">Password:</label>
        <input id="password" type="password" bind:value={password} required class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
      </div>

      <button type="submit" class="w-full py-2 px-4 bg-blue-600 text-white font-semibold rounded-lg hover:bg-blue-700">Login</button>

      {#if errorMessage}
        <p class="text-red-500 text-sm mt-4">{errorMessage}</p>
      {/if}
    </form>
  </div>
</div>
