<script>
  import { onMount } from 'svelte';
  import Login from './Login.svelte';
  import Tasks from './Tasks.svelte';

  let loggedIn = false;

  function handleLogin() {
    loggedIn = true;
  }

  onMount(() => {
    if (localStorage.getItem('loggedIn') === 'true') {
      loggedIn = true;
    }
  });
</script>

<main class="flex justify-center items-center min-h-screen bg-gray-100 p-4">
  {#if !loggedIn}
    <Login on:login={handleLogin} />
  {:else}
    <Tasks />
  {/if}
</main>
