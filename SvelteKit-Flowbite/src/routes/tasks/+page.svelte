<script>
    import { onDestroy, onMount } from 'svelte';
    import { PUBLIC_APIURL } from '$env/static/public';
    import { Table, Avatar, Toggle, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell, Navbar, NavBrand, NavLi, NavUl, NavHamburger, Label, Input, Checkbox, Button, GradientButton, Textarea } from 'flowbite-svelte';
    import { access_token } from '/src/stores/access_token';
    import axios from 'axios';
    let userId;
    let tasks = [];
    let newTask = { title: '', description: '', completed: false, due_date: '' };
    export let data;
    let fname;
    let lname;
    let token;
    onMount(() => {
      const token = localStorage.getItem('accessToken');
      access_token.set(token);
    });
    const unsubscribe = access_token.subscribe(value => {
        token = value;
    });
    onDestroy(() => {
      unsubscribe();
    });
    
    async function fetch_token() {
  try {
    // Replace with your Directus API URL and endpoint
    const directusApiUrl = PUBLIC_APIURL;
    const accessToken = token; // This should be securely stored and retrieved
    // Make a request to fetch the user data
    const response = await axios.get(`${directusApiUrl}/users/me`, {
      headers: {
        Authorization: `Bearer ${accessToken}`
      }
    });

    // Extract user ID
    userId = response.data.data.id;
    fname = response.data.data.first_name;
    lname = response.data.data.last_name;
  } 
  catch (error) {
    console.error('Error fetching user:', error);
  }
}
onMount(fetch_token);

    async function fetchTasks() {
      const apiUrl = PUBLIC_APIURL;
      try {
        const response = await fetch(`${apiUrl}/items/tasks`);
        if (response.ok) {
          tasks = await response.json();
        } else {
          console.error('Failed to fetch tasks');
        }
      } catch (error) {
        console.error('Fetch error:', error);
      }
    }
  
    async function addTask() {
      const apiUrl = PUBLIC_APIURL;
      try {
        const response = await fetch(`${apiUrl}/items/tasks`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`, 
          },
          body: JSON.stringify(newTask),
        });
        if (response.ok) {
          await fetchTasks(); // Refresh task list
          location.reload();
          newTask = { title: '', description: '', completed: false, due_date: '' };
        } else {
          console.error('Failed to add task');
        }
      } catch (error) {
        console.error('Fetch error:', error);
      }
    }
  
    async function toggleCompletion(task) {
      const apiUrl = PUBLIC_APIURL;
      try {
        const response = await fetch(`${apiUrl}/items/tasks/${task.id}`, {
          method: 'PATCH',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({ completed: !task.completed }),
        });
        if (response.ok) {
          await fetchTasks(); // Refresh task list
        } else {
          console.error('Failed to update task');
        }
      } catch (error) {
        console.error('Fetch error:', error);
      }
    }
  
    async function deleteTask(taskId) {
      const apiUrl = PUBLIC_APIURL;
      try {
        const response = await fetch(`${apiUrl}/items/tasks/${taskId}`, {
          method: 'DELETE',
          headers: {
          
          },
        });
        if (response.ok) {
          await fetchTasks();
          location.reload(); // Refresh task list
        } else {
          console.error('Failed to delete task');
        }
      } catch (error) {
        console.error('Fetch error:', error);
      }
    }
    function logout(){
      window.location='/';
    }
    onMount(fetchTasks);
  </script>

  <body class='body' background="bg.jpg">
    <Navbar let:hidden let:toggle class='' style='background-color:lightblue;'>
      <NavBrand href="/dashboard">
        <img
          src="icon.png"
          class="mr-3 h-6 sm:h-9"
          alt="Flowbite Logo"
        />
        <span class="self-center whitespace-nowrap text-xl font-semibold dark:text-white">
          To-Do
        </span>
      </NavBrand>
      <div class='flex md:order-2'>
        <Avatar/> <h3 style='margin-top:8px;margin-left:12px;margin-right:20px'>{fname} {lname}</h3>
        <Button size='sm' on:click={logout}>Logout</Button>
        <NavHamburger/>
      </div>
      <NavUl class='order-1'>
        <NavLi href="/dashboard">Home</NavLi>
        <NavLi href="/tasks">Tasks</NavLi>
      </NavUl>
    </Navbar>
    
   <div class="bg-white bg-opacity-50 font-bold"><h1 style='font-weight:100%; font-size:100px; text-align:center;'>Task Manager</h1></div> 
  <div style='margin-left:15px'>
    <form on:submit|preventDefault={addTask}>
    
    <Label class='space-y-2 text-xl mb-4 mt-4'> Title
      <Input style='width:20%' size='m' type="text" bind:value={newTask.title} placeholder="Task Title" required />
    </Label>
    <Label class='space-y-2 text-xl mb-4'> Description <br>
      <Textarea style='width:20%' bind:value={newTask.description} placeholder="Task Description"></Textarea>
    </Label>
    <Label class='space-y-2 text-xl mb-4'> Due Date
      <Input style='width:20%' type="date" bind:value={newTask.due_date} />
    </Label>
    <Label class='space-y-2'>
      <Button type="submit">Add Task</Button>
    </Label>
    </form>
  </div>
<br>
<Table hoverable={true} shadow>
  <TableHead>
    <TableHeadCell>Title</TableHeadCell>
    <TableHeadCell>Description</TableHeadCell>
    <TableHeadCell>Status</TableHeadCell>
    <TableHeadCell>Due Date</TableHeadCell>
    <TableHeadCell>Delete</TableHeadCell>
  </TableHead>
  <TableBody tableBodyClass="divide-y">
    {#each data.tasks as task (task.id)}
      {#if userId == task.uuid}
          <TableBodyRow>
            <TableBodyCell>{task.title}</TableBodyCell>
            <TableBodyCell>{task.description}</TableBodyCell>
            <TableBodyCell><Toggle checked={task.completed} on:change={() => toggleCompletion(task)}></Toggle></TableBodyCell>
            <TableBodyCell>{task.due_date}</TableBodyCell>
            <TableBodyCell><Button size='sm' on:click={() => deleteTask(task.id)}>Delete</Button></TableBodyCell>
          </TableBodyRow>
         
      {/if}
    {/each}
    </TableBody>
</Table> 
</body>
  
  