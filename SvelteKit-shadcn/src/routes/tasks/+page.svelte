<script>
  import { onDestroy, onMount } from 'svelte';
  import { PUBLIC_APIURL } from '$env/static/public';
  import { Button } from "$lib/components/ui/button/index.js";
  import { Input } from "$lib/components/ui/input/index.js";
  import { Label } from "$lib/components/ui/label/index.js";
  import * as Avatar from "$lib/components/ui/avatar/index.js";
  import * as Table from "$lib/components/ui/table/index.js";
  import { Textarea } from "$lib/components/ui/textarea/index.js";
  import { Switch } from "$lib/components/ui/switch/index.js";
  import { access_token } from '/src/stores/access_token';
  import axios from 'axios';
  import * as Card from "$lib/components/ui/card/index.js";
  import * as DropdownMenu from "$lib/components/ui/dropdown-menu/index.js";
  import { EllipsisVertical } from "lucide-svelte";
  import { toast } from "svelte-sonner";
  import { Toaster } from "svelte-sonner";
  import * as AlertDialog from '$lib/components/ui/alert-dialog/index.js';
  let userId;
  let tasks = [];
  let newTask = { title: '', description: '', completed: false, due_date: '' };
  let editTask = { title:'',description:'',due_date:'' };
  export let data;
  let fname;
  let lname;
  let token;
  let openDialog = false;
  let eTaskId;
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
  const directusApiUrl = PUBLIC_APIURL;
  const accessToken = token; 
  const response = await axios.get(`${directusApiUrl}/users/me`, {     // Make a request to fetch the user data
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
  alert(`Session Timed Out`);
  window.location.replace('/');
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
        toast.success("Task has been created");
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
        location.reload();
      } else {
        console.error('Failed to update task');
      }
    } catch (error) {
      console.error('Fetch error:', error);
    }
  }

  async function editFetch(taskId){
    const apiurl = PUBLIC_APIURL;
    try{
      const response = await fetch(`${apiurl}/items/tasks/${taskId}`,{
        method: "GET",
        headers:{
          'Content-Type':'application/json'
        },
      });
      if(response.ok){
        editTask = await response.json();
        openDialog = true;
        eTaskId = taskId;
        console.log(editTask);
      }
      else{
        console.error("Failed to fetch the tasks");
      }
    }
    catch(error){
      console.error("Fetch Error:",error);
    }
  }

  async function updateTask(taskId){
    const apiurl = PUBLIC_APIURL
    try{
      const response = await fetch(`${apiurl}/items/tasks/${taskId}`,{
        method: "PATCH",
        headers:{
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ title: editTask.title, description: editTask.description, due_date: editTask.due_date }),
      });
      if(response.ok){
        await fetchTasks();
        location.reload();
        toast.success("Updated the task");
        openDialog = false;
      }
      else{
        alert(`Failed to Edit the task`);
        openDialog = false;
        console.error("Failed to Edit the task");
      }
    }
    catch(error){
      console.error("Fetch Error:",error);
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
        toast.success("Successfully Deleted the Task");
      } else {
        console.error('Failed to delete task');
      }
    } catch (error) {
      console.error('Fetch error:', error);
    }
  }
  async function logout(){
    const response = await fetch(`${PUBLIC_APIURL}/auth/logout`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`,
      },
      body : JSON.stringify({refresh_token:token}),
    });
    if(response.ok){
      alert(`Successfully Logged out`);
      localStorage.removeItem('accessToken');
      console.log("Successfully Logged out");
      window.location.replace("/");
    
    }
      else{
      console.error("Error:",error);
    }
  }
  onMount(fetchTasks);
</script>

<body class='body' background="bg.jpg">
  <nav class='flex items-center h-14 space-x-4 lg:space-x-6 bg-[#38bdf8]'>
    <Avatar.Root class="ml-4 mr-1 ">
      <Avatar.Image src="icon.png" alt="todo-icon"></Avatar.Image>
    </Avatar.Root>
    <a href='/dashboard' class="mr-10 text-xl font-medium">To-Do</a> 
  <a href="/dashboard" class="text-muted-foreground hover:text-primary text-md font-medium transition-colors" >Home</a>
  <a href="/tasks" class="text-foreground hover:text-primary text-md font-medium transition-colors" >Tasks</a>
  <Button variant="default" class="bg-[#1d4ed8]" style="margin-left:auto;margin-right:20px" on:click={logout}>Logout</Button>
</nav>
<div class="flex">
  <div class="opacity-80">
    <Card.Root class="w-max h-[910px]" style="margin-right:auto;">
      <Card.Header>
        <Card.Title class="text-xl">Task Details</Card.Title>
      </Card.Header>
      <Card.Content>
        <div style='margin-left:15px;'>
          <form on:submit|preventDefault={addTask}>
            <div class="mt-4 mb-8">
              <Label class='text-xl'> Title
              <Input id="title" class="mt-2" style='width:max' type="text" bind:value={newTask.title} placeholder="Task Title" required /> 
            </Label>
            </div>
            <div class="mb-8">
              <Label class='text-xl'> Description 
              <Textarea id="description" class="mt-2" style='width:max' bind:value={newTask.description} placeholder="Task Description"></Textarea>
              </Label>
            </div>
            <div class="mb-10">
              <Label for="due_date" class='text-xl'> Due Date</Label>
              <Input id="due_date" class="mt-2" style='width:max;' type="date" bind:value={newTask.due_date}/>
            </div>
            <div>
              <Toaster id="toast"/>
              <Button class="mt-4" type="submit">Add Task</Button>
            </div>
          </form>
        </div>
      </Card.Content>
      <Card.Footer></Card.Footer>
    </Card.Root>
  </div>
  <div class="w-full mt-2" >
    <Table.Root class="w-[98%] bg-white ml-4 rounded" style="opacity:80%;">
      <Table.Header>
        <Table.Row>
          <Table.Head>Title</Table.Head>
          <Table.Head>Description</Table.Head>
          <Table.Head>Status</Table.Head>
          <Table.Head>Due Date</Table.Head>
          <Table.Head>Options</Table.Head>
        </Table.Row>
      </Table.Header>
      <Table.Body>
          {#each data.tasks as task (task.id)}
            {#if userId == task.uuid}
              <Table.Row>
                <Table.Cell>{task.title}</Table.Cell>
                <Table.Cell>{task.description}</Table.Cell>
                <Table.Cell><Switch checked={task.completed} on:click={toggleCompletion(task)}></Switch></Table.Cell>
                <Table.Cell>{task.due_date}</Table.Cell>
                <Table.Cell>
                  <DropdownMenu.Root>
                    <DropdownMenu.Trigger asChild let:builder>
                      <Button variant="outline" class="ml-auto" style="border:none" builders={[builder]}><EllipsisVertical/></Button>
                    </DropdownMenu.Trigger>
                    <DropdownMenu.Content>
                      <DropdownMenu.CheckboxItem on:click={editFetch(task.id)}>Edit</DropdownMenu.CheckboxItem>
                      <Toaster/>
                      <DropdownMenu.CheckboxItem on:click={deleteTask(task.id)}>Delete</DropdownMenu.CheckboxItem>
                    </DropdownMenu.Content>
                  </DropdownMenu.Root>
                  <AlertDialog.Root open={openDialog}>
                    <AlertDialog.Content>
                      <AlertDialog.Header>
                        <AlertDialog.Title>Edit Task</AlertDialog.Title>
                        <AlertDialog.Description>
                          <Label for="newTitle" class="font-bold text-md mt-2">Title</Label>
                            <Input id="newTitle" class="mt-2" type="text" bind:value={editTask.title}/>
                          <Label for="newDesc" class="font-bold mt-2">Description</Label>
                          <Input id="newDesc" type="text" bind:value={editTask.description}/>
                          <Label for="newDate" class="font-bold mt-2">Due Date</Label>
                          <Input id="newDate" type="date" bind:value={editTask.due_date}/>
                        </AlertDialog.Description>
                      </AlertDialog.Header>
                      <AlertDialog.Footer>
                        <AlertDialog.Action on:click={updateTask(eTaskId)}>Submit</AlertDialog.Action>
                        <AlertDialog.Cancel on:click={()=>{openDialog=false}}>Cancel</AlertDialog.Cancel>
                      </AlertDialog.Footer>
                    </AlertDialog.Content>
                  </AlertDialog.Root>
                </Table.Cell>
              </Table.Row>
            {/if}
          {/each}
      </Table.Body>
    </Table.Root> 
  </div>
</div>
</body>
