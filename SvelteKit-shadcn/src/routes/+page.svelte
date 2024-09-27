<script>
  import * as Avatar from "$lib/components/ui/avatar/index.js";
  import * as AlertDialog from "$lib/components/ui/alert-dialog/index.js";
  import {PUBLIC_APIURL} from '$env/static/public';
  import * as Carousel from "$lib/components/ui/carousel/index.js";
  import { Button,buttonVariants } from "$lib/components/ui/button/index.js";
  import { Label } from "$lib/components/ui/label/index.js";
  import { Input } from "$lib/components/ui/input/index.js";
  import { X } from 'lucide-svelte';
  import Autoplay from "embla-carousel-autoplay";
  import * as Card from "$lib/components/ui/card";
  
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
          
          window.location.href='/tasks';// Handle successful login
        } else {
          const err = await response.json();
          const error = `Login failed: ${err.message}`;
          alert(`Login Failed, Ensure that credentials are valid`);
          console.log(error);
        }
      }
   
  
  const plugin = Autoplay({ delay:3000 });
  function signup(){
    window.location = '/register';
  }
  let showPassword = false;
  function handleCheckboxChange(event){
      showPassword = event.target.checked;
  }
  </script>
  
  <body class='body' background="bg.jpg">
    <nav class='flex items-center h-14 space-x-4 lg:space-x-6 bg-[#38bdf8]'>
      <Avatar.Root class="ml-4 mr-1 ">
        <Avatar.Image src="icon.png" alt="todo-icon"></Avatar.Image>
      </Avatar.Root>
      <a href='/' class="mr-10 text-xl font-medium">To-Do</a> 
    <a href="/" class="text-foreground hover:text-primary text-md font-medium transition-colors" >Home</a>
    <AlertDialog.Root >
      <AlertDialog.Trigger style="margin-left:auto;">
        <Button class="bg-[#b91c1c]" variant="destructive">Login</Button>
      </AlertDialog.Trigger>
      <AlertDialog.Content>
        <AlertDialog.Header> 
          <div class="flex">
          <AlertDialog.Title><h1>Login</h1></AlertDialog.Title>
          <AlertDialog.Cancel class="text-muted-foreground" style="margin-left:auto;border:none"><X/></AlertDialog.Cancel>
        </div>
          <AlertDialog.Description>
            <form class='flex flex-col space-y 6' action='#'>
              <Label class='space-y-2'>
                <span>Email</span>
                <Input type='email' name='email' bind:value={email} placeholder='name@gmail.com' required/>
              </Label>
              <Label class='space-y-2' style='margin-top:15px;'>
                <span>Password</span>
                <Input type={showPassword ? 'text' : 'password'} name='password' bind:value={password} placeholder='*********' required/>
              </Label>
              <div class="flex items-start" style='margin-top:15px;'>
                <Input type="checkbox" class="ml-2 w-4" checked={showPassword} on:change={handleCheckboxChange}/><span class="ml-2 mt-2.5 text-bold text-sm">Show Password</span>
              </div>
              <Button type="submit" on:click={handleSubmit} color='purple'  class="w-full1" style='margin-top:15px;'>Login to your account</Button>
              <div class="text-sm font-medium text-gray-500 dark:text-gray-300" style='margin-top:15px;'>
                Not registered? <a href="/register" class="text-primary-700 hover:underline dark:text-primary-500"> Create account </a>
              </div>
            </form>
          </AlertDialog.Description>
        </AlertDialog.Header>
        <AlertDialog.Footer>
        </AlertDialog.Footer>
      </AlertDialog.Content>
    </AlertDialog.Root>
      <Button variant="default" class="bg-[#1d4ed8]" style="margin-right:20px" on:click={signup}>Signup</Button>
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