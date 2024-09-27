<script>
    import { ArrowRightOutline } from 'flowbite-svelte-icons';
    import { Label } from "$lib/components/ui/label";
    import { Input } from "$lib/components/ui/input";
    import * as Card from "$lib/components/ui/card/index";
    import { Button } from "$lib/components/ui/button";
    import * as Avatar from "$lib/components/ui/avatar/index";
    import {PUBLIC_APIURL} from '$env/static/public';
    import { EyeIcon } from 'lucide-svelte';
    import { EarOffIcon } from 'lucide-svelte';
 
   let users = {fname:"",lname:"",email:"", password:""};
    async function register(){
        const apiurl = PUBLIC_APIURL;
        try{
            const response = await fetch(`${apiurl}/users/register`,{
                method:'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ "first_name":users.fname, "last_name":users.lname, "email":users.email, "password":users.password }),
            });
            if (response.ok) {
            console.log('Registration successful');
            alert(`Registration Successful`);
            window.location='/';
            } 
            else {
                console.error('Error:');
            }
        }
        catch(error){
            console.error(`Could not register:`,error);
        }

    }

let showPassword = false;
function handleCheckboxChange(event){
    showPassword = event.target.checked;
}
</script>
<body background='rg-bg.png' style='background-size:80%; background-position: right; background-repeat:no-repeat;'>
<div  class="space-y-2" style="height:1000px; width:450px;">
    <Card.Root class='!max-w-full h-full'>
        <Card.Header>
        <div class="flex">    
        <Avatar.Root>
        <Avatar.Image src='icon.png' alt='todo icon'/>
        </Avatar.Root>
        <span class='mt-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white' style="float:inline-start; margin-left:10px;">To-Do</span>
        </div>
        <Card.Title>
            <h5 class= 'mt-20 mb-5 ml-2 text-3xl font-bold tracking-tight text-gray-900 dark:text-white'>Register</h5>
            </Card.Title>
        <Card.Description></Card.Description>
        </Card.Header>
        <Card.Content>
        <form>
            <div class="mb-4">
                <Label class="ml-2 space-y-2"><span class='text-lg'>First Name</span>
                    <Input class="ml-2 w-max fname" bind:value={users.fname} type='text' placeholder='First Name' required></Input>
                </Label>
            </div>
            <div class="mb-4">
                <Label class='ml-2 space-y-2'><span class='text-lg'>Last Name</span>
                    <Input class="ml-2 w-max lname" bind:value={users.lname} type='text' placeholder='Last Name' required></Input>
                </Label>
            </div>
            <div class="mb-4">
                <Label class='ml-2 space-y-2'><span class='text-lg'>Email</span>
                    <Input class="ml-2 w-max email space-y-4" bind:value={users.email} type='text' placeholder='abc@example.com' required></Input>
                </Label>
            </div>
            <div>
                <Label class='ml-2 mb-2 space-y-2'><span class='text-lg'>Password</span>
                    <Input id='pass' class="ml-2 w-max password" bind:value={users.password} type={showPassword ? 'text' : 'password'} placeholder='***********' required></Input>
                </Label>
            </div>
            <div class="flex">
            <Input id="showhide" type="checkbox" class="ml-2 w-4" checked={showPassword} on:change={handleCheckboxChange}></Input><span class="ml-2 mt-2.5 text-bold text-sm">Show Password</span>
            </div>
        </form>
        </Card.Content>
        <Card.Footer>
            <Button on:click={register} color='blue' class='ml-2 mt-4'>
                Submit <ArrowRightOutline class="w-6 h-6 ms-2 text-white" />
                </Button>
        </Card.Footer>
    </Card.Root>
</div>
</body>

