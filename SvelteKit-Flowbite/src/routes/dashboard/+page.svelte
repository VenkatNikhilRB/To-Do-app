<script>
    import { Navbar,Avatar, Carousel , Card, NavBrand, NavLi, NavUl, NavHamburger, Label, Input, Checkbox, Button, GradientButton, Modal } from 'flowbite-svelte';
    import {PUBLIC_APIURL} from '$env/static/public';
    import { fade } from 'svelte/transition';
    import { onMount,onDestroy } from 'svelte';
    import { access_token } from '/src/stores/access_token';
    import axios from 'axios';
    let accessToken='';
    let fname;
    let lname;
    let token;
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
    let images = [
      {
        alt: "Welcome",
        src: "slide-1.png"
      },
      {
        alt: "Question",
        src: "slide-2.png"
      },
      {
        alt: "Options",
        src: "slide-3.png"
      }
    ];
      const Animation = (x) => fade(x,{duration:600})
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
        <Avatar/><h3 style="margin-top: 8px; margin-left:12px; margin-right:15px">{fname} {lname}</h3>
        <Button color='red' on:click={() => (window.location.href='/')} size='sm'>Logout</Button>

        <NavHamburger/>
      </div>

      <NavUl class='order-1'>
        <NavLi href="/dashboard">Home</NavLi>
        <NavLi href="/tasks">Tasks</NavLi>
      </NavUl>
    </Navbar>
    <div class="h-screen flex justify-center items-center">
      <Card size='xl' style='margin-bottom:50px;height:800px;' class='max-w-screen-2xl justify-center bg-opacity-60'>
        <Carousel {images} style="height:770px" transition={Animation} duration="4000"/>
      </Card>
    </div>
    </body>
    <style>
     
</style>