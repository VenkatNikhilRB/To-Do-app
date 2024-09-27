<script>
  import {PUBLIC_APIURL} from '$env/static/public';
  import { fade } from 'svelte/transition';
  import { onMount,onDestroy } from 'svelte';
  import { Button } from "$lib/components/ui/button/index.js";
  import * as Carousel from "$lib/components/ui/carousel/index.js";
  import * as Card from "$lib/components/ui/card/index.js";
  import * as Avatar from "$lib/components/ui/avatar/index.js";
  import Autoplay from "embla-carousel-autoplay";
  import { access_token } from '/src/stores/access_token';
  import axios from 'axios';

  let accessToken='';
  let fname;
  let lname;
  let token;
  const plugin = Autoplay({ delay:3000 });

  onMount(() => {
    const token = localStorage.getItem('accessToken');
    access_token.set(token);
  });
  const unsubsribe = access_token.subscribe(value => {
    token = value;
  })
  onDestroy(() => {
    unsubsribe();
  });
  async function username(){
    const response = await axios.get(`${PUBLIC_APIURL}/users/me`,{
      headers: {
        Authorization: `Bearer ${token}`
      }
    });
    fname = response.data.data.first_name;
    lname = response.data.data.last_name;
  }
  onMount(username);
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
      window.history.pushState(null,null,window.location.href='/');
      window.history.go(-window.history.length);
    }
    else{
      console.error("Error:",error);
    }
  }
</script>

<body class='body' background="bg.jpg">
  <nav class='flex items-center h-14 space-x-4 lg:space-x-6 bg-[#38bdf8]'>
    <Avatar.Root class="ml-4 mr-1 ">
      <Avatar.Image src="icon.png" alt="todo-icon"></Avatar.Image>
    </Avatar.Root>
    <a href='/dashboard' class="mr-10 text-xl font-medium">To-Do</a> 
    <a href="/dashboard" class="text-foreground hover:text-primary text-md font-medium transition-colors" >Home</a>
    <a href="/tasks" class="text-muted-foreground hover:text-primary text-md font-medium transition-colors" >Tasks</a>
    <Button variant="default" class="bg-[#1d4ed8]" style="margin-left:auto;margin-right:20px" on:click={logout}>Logout</Button>
  </nav>
  <div class="h-screen flex justify-center items-center">
    <Card.Root size='xl' style='margin-bottom:50px;height:800px;' class='max-w-screen-2xl justify-center bg-opacity-60'>
      <Card.Content>
          <Carousel.Root plugins={[plugin]}>
            <Carousel.Content>
              <Carousel.Item>
                <img src="slide-1.png" alt="Welcome"/>
              </Carousel.Item>
              <Carousel.Item>
                <img src="slide-2.png" alt="Question"/>
              </Carousel.Item>
              <Carousel.Item>
                <img src="slide-3.png" alt="Options"/>
              </Carousel.Item>
            </Carousel.Content>
          </Carousel.Root>
      </Card.Content>
    </Card.Root>
  </div>
</body>
  