<script>
  import { onMount } from 'svelte';
  let tasks = [];
  let title = '';
  let description = '';
  let status = '';
  let dueDate = '';
  let errorMessage = '';
  let selectedTask = null;

  async function fetchTasks() {
    try {
      const response = await fetch('http://localhost:8055/items/Tasks', {
        method: 'GET',
        headers: {
          Authorization: `Bearer ${localStorage.getItem('token')}`,
        },
      });
      if (response.ok) {
        const data = await response.json();
        tasks = data.data;
      } else {
        errorMessage = 'Failed to fetch tasks';
      }
    } catch (error) {
      errorMessage = 'An error occurred while fetching tasks';
    }
  }

  async function addTask() {
    try {
      const response = await fetch('http://localhost:8055/items/Tasks', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`,
        },
        body: JSON.stringify({ title, description, status, due_date: dueDate }),
      });
      if (response.ok) {
        fetchTasks();
        title = '';
        description = '';
        status = '';
        dueDate = '';
      } else {
        errorMessage = 'Failed to add task';
      }
    } catch (error) {
      errorMessage = 'An error occurred while adding the task';
    }
  }

  async function deleteTask(id) {
    try {
      const response = await fetch(`http://localhost:8055/items/Tasks/${id}`, {
        method: 'DELETE',
        headers: {
          Authorization: `Bearer ${localStorage.getItem('token')}`,
        },
      });
      if (response.ok) {
        fetchTasks();
      } else {
        errorMessage = 'Failed to delete task';
      }
    } catch (error) {
      errorMessage = 'An error occurred while deleting the task';
    }
  }

  async function updateTask(id, updatedTask) {
    try {
      const response = await fetch(`http://localhost:8055/items/Tasks/${id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`,
        },
        body: JSON.stringify(updatedTask),
      });
      if (response.ok) {
        fetchTasks();
        selectedTask = null;
      } else {
        errorMessage = 'Failed to update task';
      }
    } catch (error) {
      errorMessage = 'An error occurred while updating the task';
    }
  }

  function handleLogout() {
    localStorage.removeItem('loggedIn');
    localStorage.removeItem('token');
    window.location.reload();
  }

  function selectTask(task) {
    selectedTask = task;
    title = task.title;
    description = task.description;
    status = task.status;
    dueDate = task.due_date;
  }
</script>

<div class="min-h-screen bg-gray-100 p-4 md:p-8 lg:p-12">
  <button class="fixed top-4 right-4 bg-red-600 text-white py-2 px-4 rounded-lg hover:bg-red-700" on:click={handleLogout}>Logout</button>

  <h2 class="text-2xl md:text-3xl lg:text-4xl font-bold text-gray-800 mb-6">Task Management</h2>

  {#if errorMessage}
    <p class="text-red-500 mb-4">{errorMessage}</p>
  {/if}

  <div class="bg-white p-4 md:p-6 lg:p-8 rounded-lg shadow-lg mb-6 max-w-2xl mx-auto">
    <input type="text" placeholder="Task Title" bind:value={title} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
    <textarea placeholder="Task Description" bind:value={description} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600"></textarea>
    <select bind:value={status} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600">
      <option value="" disabled>Status</option>
      <option value="pending">pending</option>
      <option value="inprogress">inprogress</option>
      <option value="completed">completed</option>
    </select>
    <input type="date" placeholder="Due Date" bind:value={dueDate} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
    <button class="w-full bg-green-600 text-white py-2 rounded-lg hover:bg-green-700" on:click={addTask}>Add Task</button>
  </div>

  <div class="flex space-x-4 mb-6 justify-center">
    <button class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700" on:click={fetchTasks}>Get Task</button>
    {#if selectedTask}
      <button class="bg-yellow-600 text-white py-2 px-4 rounded-lg hover:bg-yellow-700" on:click={() => updateTask(selectedTask.id, { title, description, status, due_date: dueDate })}>Update</button>
    {/if}
  </div>

  <div class="overflow-auto max-h-96">
    {#if tasks.length}
      <ul class="space-y-4">
        {#each tasks as task (task.id)}
          <li class="bg-white p-4 rounded-lg shadow-lg">
            <h3 class="text-lg md:text-xl lg:text-2xl font-bold mb-2">{task.title}</h3>
            <p class="text-gray-700 mb-2">{task.description}</p>
            <p class="text-gray-500 mb-2">Status: {task.status}</p>
            <p class="text-gray-500 mb-4">Due Date: {task.due_date}</p>
            <div class="flex space-x-4">
              <button class="bg-yellow-600 text-white py-2 px-4 rounded-lg hover:bg-yellow-700" on:click={() => selectTask(task)}>Edit</button>
              <button class="bg-red-600 text-white py-2 px-4 rounded-lg hover:bg-red-700" on:click={() => deleteTask(task.id)}>Delete</button>
            </div>
          </li>
        {/each}
      </ul>
    {/if}
  </div>
</div>
