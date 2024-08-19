<script>
  import { onMount } from 'svelte';

  // Variables for the add form
  let newTitle = '';
  let newDescription = '';
  let newStatus = '';
  let newDueDate = '';

  // Variables for the update form
  let editTitle = '';
  let editDescription = '';
  let editStatus = '';
  let editDueDate = '';
  let selectedTask = null;

  let tasks = [];
  let errorMessage = '';

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
        body: JSON.stringify({ title: newTitle, description: newDescription, status: newStatus, due_date: newDueDate }),
      });
      if (response.ok) {
        fetchTasks();
        newTitle = '';
        newDescription = '';
        newStatus = '';
        newDueDate = '';
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

  async function updateTask(id) {
    try {
      const response = await fetch(`http://localhost:8055/items/Tasks/${id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`,
        },
        body: JSON.stringify({
          title: editTitle,
          description: editDescription,
          status: editStatus,
          due_date: editDueDate,
        }),
      });
      if (response.ok) {
        fetchTasks();
        selectedTask = null;
        clearEditForm();
      } else {
        errorMessage = 'Failed to update task';
      }
    } catch (error) {
      errorMessage = 'An error occurred while updating the task';
    }
  }

  function clearEditForm() {
    editTitle = '';
    editDescription = '';
    editStatus = '';
    editDueDate = '';
  }

  function selectTask(task) {
    selectedTask = task;
    editTitle = task.title;
    editDescription = task.description;
    editStatus = task.status;
    editDueDate = task.due_date;
  }

  function handleLogout() {
    localStorage.removeItem('loggedIn');
    localStorage.removeItem('token');
    window.location.reload();
  }
</script>

<div class="min-h-screen bg-gray-100 p-4 md:p-8 lg:p-12">
  <button class="fixed top-4 right-4 bg-red-600 text-white py-2 px-4 rounded-lg hover:bg-red-700" on:click={handleLogout}>Logout</button>

  <h2 class="text-2xl md:text-3xl lg:text-4xl font-bold text-gray-800 mb-6">Task Management</h2>

  {#if errorMessage}
    <p class="text-red-500 mb-4">{errorMessage}</p>
  {/if}

  <!-- Add Task Form -->
  <div class="bg-white p-4 md:p-6 lg:p-8 rounded-lg shadow-lg mb-6 max-w-2xl mx-auto">
    <input type="text" placeholder="Task Title" bind:value={newTitle} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
    <textarea placeholder="Task Description" bind:value={newDescription} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600"></textarea>
    <select bind:value={newStatus} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600">
      <option value="" disabled>Status</option>
      <option value="pending">pending</option>
      <option value="inprogress">inprogress</option>
      <option value="completed">completed</option>
    </select>
    <input type="date" placeholder="Due Date" bind:value={newDueDate} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
    <button class="w-full bg-green-600 text-white py-2 rounded-lg hover:bg-green-700" on:click={addTask}>Add Task</button>
  </div>

  <div class="flex space-x-4 mb-6 justify-center">
    <button class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700" on:click={fetchTasks}>Refresh Tasks</button>
  </div>

  <!-- Tasks List -->
  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
    {#each tasks as task}
      <div class="bg-white p-4 rounded-lg shadow-lg">
        <h3 class="text-xl font-semibold text-gray-800 mb-2">{task.title}</h3>
        <p class="text-gray-600 mb-2">{task.description}</p>
        <p class="text-gray-600 mb-2">Status: {task.status}</p>
        <p class="text-gray-600 mb-4">Due Date: {task.due_date}</p>
        <div class="flex justify-between">
          <button class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700" on:click={() => selectTask(task)}>Edit</button>
          <button class="bg-red-600 text-white py-2 px-4 rounded-lg hover:bg-red-700" on:click={() => deleteTask(task.id)}>Delete</button>
        </div>
      </div>
    {/each}
  </div>

  <!-- Update Task Form -->
  {#if selectedTask}
    <div class="bg-white p-4 md:p-6 lg:p-8 rounded-lg shadow-lg mt-6 max-w-2xl mx-auto">
      <input type="text" placeholder="Task Title" bind:value={editTitle} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
      <textarea placeholder="Task Description" bind:value={editDescription} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600"></textarea>
      <select bind:value={editStatus} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600">
        <option value="" disabled>Status</option>
        <option value="pending">pending</option>
        <option value="inprogress">inprogress</option>
        <option value="completed">completed</option>
      </select>
      <input type="date" placeholder="Due Date" bind:value={editDueDate} class="w-full px-3 py-2 mb-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600" />
      <button class="w-full bg-yellow-600 text-white py-2 rounded-lg hover:bg-yellow-700" on:click={() => updateTask(selectedTask.id)}>Update Task</button>
      <button class="w-full mt-4 bg-gray-600 text-white py-2 rounded-lg hover:bg-gray-700" on:click={() => { selectedTask = null; clearEditForm(); }}>Cancel</button>
    </div>
  {/if}
</div>
