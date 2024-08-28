<script>
import { fade } from 'svelte/transition';
import { Navbar, Carousel , Card, NavBrand, NavLi, NavUl, NavHamburger, Label, Input, Checkbox, Button, GradientButton, Modal } from 'flowbite-svelte';
import {PUBLIC_APIURL} from '$env/static/public';
let defaultModal=false;
let email;
let password;
let accessToken='';
async function handleSubmit() {
      const response = await fetch(`${PUBLIC_APIURL}/auth/login`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json', 
        },
        body: JSON.stringify({ email, password }),
      });
  
      if (response.ok) {
        const data = await response.json();
        console.log('Login successful:', data);
        accessToken = data.data.access_token;
        localStorage.setItem('accessToken', accessToken);
        
        window.location.href='/tasks';// Handle successful login (e.g., redirect to dashboard)
      } else {
        const err = await response.json();
        const error = `Login failed: ${err.message}`;
        alert(`Login Failed, Ensure that credentials are valid`);
        console.log(error);
      }
    }
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
function signup(){
  window.location = '/register';
}
</script>
<body class='body' background="bg.jpg">
    <Navbar let:hidden let:toggle class='' style='background-color:lightblue;'>
      <NavBrand href="/">
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
        <Button on:click={() => (defaultModal=true)} size='sm'>Sign-in</Button>
        <GradientButton color='blue' style='margin-left:15px' on:click={signup} size='sm'>Sign-up</GradientButton>

        <NavHamburger/>
      </div>

      <NavUl class='order-1'>
        <NavLi href="/" active={true}>Home</NavLi>
      </NavUl>
    </Navbar>
    <div class="h-screen flex justify-center items-center">
        <Card size='xl' style='margin-bottom:50px;height:800px;' class='max-w-screen-2xl justify-center bg-opacity-60'>
          <Carousel {images} style="height:770px" transition={Animation} duration="4000"/>
        </Card>
      </div>
    <Modal bind:open={defaultModal} size='xs' autoclose={false} class='w-full'>
      <form class='flex flex-col space-y 6' action='#'>
        <h3 class='mb-4 text-xl font-medium text-gray-900 dark:text-white'>Sign in</h3>
        <Label class='space-y-2'>
          <span>Email</span>
          <Input type='email' name='email' bind:value={email} placeholder='name@gmail.com' required/>
        </Label>
        <Label class='space-y-2' style='margin-top:15px;'>
          <span>Password</span>
          <Input type='password' name='password' bind:value={password} placeholder='********' required/>
        </Label>
        <div class="flex items-start" style='margin-top:15px;'>
          <Checkbox>Remember me</Checkbox>
        </div>
        <GradientButton type="submit" on:click={handleSubmit} color='purple'  class="w-full1" style='margin-top:15px;'>Login to your account</GradientButton>
        <div class="text-sm font-medium text-gray-500 dark:text-gray-300" style='margin-top:15px;'>
          Not registered? <a href="/register" class="text-primary-700 hover:underline dark:text-primary-500"> Create account </a>
        </div>
      </form>
    </Modal>
    </body>