<script>
  import { onMount } from 'svelte';
  import Login from './Login.svelte';
  import Tasks from './Tasks.svelte';

  let loggedIn = false;

  // Function to handle login
  function handleLogin() {
    loggedIn = true;
  }

  // Redirect to task management if already logged in
  onMount(() => {
    if (localStorage.getItem('loggedIn') === 'true') {
      loggedIn = true;
    }
  });
</script>

<main>
  {#if !loggedIn}
    <Login on:login={handleLogin} />
  {:else}
    <Tasks />
  {/if}
</main>

<style>
  main {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f3f4f6;
  }
</style>
